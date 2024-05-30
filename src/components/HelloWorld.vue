<template>
  <div>
    <div class="drop-zone" @dragover.prevent>
      <div
          v-for="item in getList(1)"
          :key="item.id"
          class="drag-el"
          draggable="true"
          @dragstart="startDrag($event, item)"
          @drop="onDrop($event, 1)"
          @dragover.prevent
      >
        {{ item.name }}
      </div>
    </div>

    <div class="drop-zone" @dragover.prevent>
      <div
          v-for="item in getList(2)"
          :key="item.id"
          class="drag-el"
          draggable="true"
          @dragstart="startDrag($event, item)"
          @drop="onDrop($event, 2)"
          @dragover.prevent
      >
        {{ item.name }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const getList = (listId) => {
  return itemsList.value.filter((item) => item.listId === listId);
};

const startDrag = (event, item) => {
  event.dataTransfer.dropEffect = "move";
  event.dataTransfer.effectAllowed = "move";
  event.dataTransfer.setData("itemId", item.id);
};

const onDrop = (event, list) => {
  const itemId = event.dataTransfer.getData("itemId");
  const item = itemsList.value.find((item) => item.id.toString() === itemId);
  if (item) {
    item.listId = list;
  }
};

const itemsList = ref([
  { id: 0, name: "item=a", listId: 1 },
  { id: 1, name: "item=b", listId: 2 },
  { id: 2, name: "item=c", listId: 2 },
  { id: 3, name: "item=d", listId: 1 },
  { id: 4, name: "item=e", listId: 1 },
]);
</script>
<style lang="scss" scoped>
.drop-zone{
  width: 700px;
  height: auto;
  background-color: #2c3e50;
  margin-bottom: 25px;
  padding: 25px;
  .drag-el{
    user-select: none;
    margin-bottom: 10px;
    width: 100%;
    height: 40px;
    background-color: #f2f2f2;

  }
}
</style>
