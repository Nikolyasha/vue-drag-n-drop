<script>
import {ref} from "vue";

export default {
  data() {
    return {
      items: [{ id: 0, title: 'Item 1', list: 0, },{ id: 1, title: 'Item 2', list: 0, }],
      columns: [],
      order:null,
      currentDraggableElemId:null
    }
  },
  computed: {
    // getList(listId){
    //   return this.items.filter((item) => item.list === listId)
    // }
  },

  methods: {
    getList(listId){
      return this.items.filter((item) => item.list === listId)
    },
    getColumns(index){
      return this.columns.filter((column) => column.list === index)
    },
    startDrag(evt, item , indexStartElem) {
      evt.dataTransfer.dropEffect = 'move'
      evt.dataTransfer.effectAllowed = 'move'
      evt.dataTransfer.setData('itemID', item.id)
      console.log(indexStartElem)
      this.currentDraggableElemId = item.id
    },
    onDrop(evt, list) {
      this.$refs.placeholderElem.forEach((el)=>{
        el.classList.remove("placeholder-elem-active")
        el.style.height = 0
      })
      const itemID = evt.dataTransfer.getData('itemID')
      const item = this.items.find((item) => item.id == itemID)
      item.list = list

      console.log("1:",this.items[this.currentDraggableElemId])
      console.log("2:",this.items[this.order])
      console.log("3:",this.items)

      let bank = this.items[this.currentDraggableElemId]
      this.items[this.currentDraggableElemId] =  this.items[this.order]
      this.items[this.order] = bank

      let bankId = this.items[this.currentDraggableElemId].id
      this.items[this.currentDraggableElemId].id =  this.items[this.order].id
      this.items[this.order].id = bankId

    },
    onDragEnter(elem , placeholder , item){
      console.log('enter-id: '+item+'  draggable-id: '+ this.currentDraggableElemId)
      this.order =item
    },
    onDragLeave(elem,placeholder){

    },
    showObj(){
      console.log(this.items);
      console.log(this.$refs.dragEl);
    },
    addColumn(){
      this.columns.push({
        id: this.columns.length + 1,
        name: 'column-'+this.columns.length
      })
      console.log(this.columns)
    },
    addElem(index){
      this.items.push({
        id: this.items.length,
        title: "item-"+(this.items.length+1),
        list: index,
      })
    },
  },
}
</script>

<template>
  <div class="columns-count-wrapper">
    <p>добавить столбец</p>
    <button @click="addColumn">add column</button>
  </div>
  <div class="wrapper-drop-zone" ref="wrapperDropZone">
    <div class="drop-zone"
         v-for="(column,index) in getColumns(index)"
         :key="index"
         ref="dropZone"
         @drop="onDrop($event, index)"
         @dragover.prevent
         @dragenter.prevent>
      <div class="el-wrapper">
        <button class="button-create-elem" @click="addElem(index)">add elem in col {{index}}</button>
        <div :class="'drag-el'+' '+'order-'+item.order"
             v-for="item in getList(index)"
             :key="item.title"
             draggable="true"
             @dragstart="startDrag($event, item ,this.$refs.dragEl.indexOf(item))"
             @dragenter="onDragEnter(this.$refs.dragEl[item.id] , this.$refs.placeholderElem[item.id] , item.id)"
             @dragleave="onDragLeave(this.$refs.dragEl[item.id] , this.$refs.placeholderElem[item.id])"
             ref="dragEl">
          <div class="placeholder-elem " ref="placeholderElem"></div>
          <div class="content">
            <div class="">{{ item.title }}</div>
          </div>
        </div>
      </div>
      <div v-if="getList(index).length === 0" class="placeholder"></div>
    </div>

  </div>
  <button @click="showObj">button</button>
</template>


<style lang="scss" scoped>

.wrapper-drop-zone {
  width: fit-content;
  min-height: 400px;
  display: flex;
  gap: 20px;
}
.drop-zone {
  width: 200px;
  background-color: #eee;
  margin-bottom: 10px;
  padding: 10px;
  //height: fit-content;
  height: 400px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.drag-el {

  //margin-bottom: 10px;
  padding: 5px 0;
  //height: fit-content;
  //border: 1px solid green;
}

.button-create-elem{
  margin-bottom: 0;
}

.placeholder{
  margin-bottom: 10px;
  padding: 5px;
  height: max-content;
  //border: 1px solid red;
}
.placeholder-elem-active{
  width: 100%;
  padding-bottom: 10px;
  height: max-content;
  //border: 1px solid red;
  pointer-events: none;
}
.content{

  padding: 5px 0 ;
  background-color: #fff;
  width: 100%;
  height: 100%;
  pointer-events: none;
}
</style>
