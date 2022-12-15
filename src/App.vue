<script setup>
import { ref } from "vue";

const users = ref([]);
const dragIndex = ref(null);

const getUsers = async () => {
  const response = await fetch("https://jsonplaceholder.typicode.com/users");
  const data = await response.json();
  users.value = data;
};

getUsers();

const pushUser = () => {
  users.value.push({
    id: 0,
    name: "",
    username: "",
    email: "",
  });
};

const dragStart = (index) => {
  dragIndex.value = index;
};

const dragEnter = (index) => {
  if (index === dragIndex.value) return;
  const deleteElement = users.value.splice(dragIndex.value, 1)[0];
  users.value.splice(index, 0, deleteElement);
  dragIndex.value = index;
};

const dragEnd = (ev) => {
  dragIndex.value = null;
};

const sendUsers = () => {
  // 並び替え後のusers配列をサーバに送信する処理を追加する
  console.table(users.value);
};
</script>

<template>
  <button @click="pushUser">ユーザーを追加</button>
  <button @click="sendUsers">変更を確定する</button>
  <table>
    <thead>
      <tr>
        <th>ID</th>
        <th>名前</th>
        <th>ユーザ名</th>
        <th>Email</th>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="(user, index) in users"
        :key="user.id"
        :draggable="true"
        @dragstart="dragStart(index)"
        @dragenter="dragEnter(index)"
        @dragover.prevent
        @dragend="dragEnd"
        :class="index === dragIndex ? 'dragging' : ''"
      >
        <td>{{ user.id !== 0 ? user.id : null }}</td>
        <td><input type="text" v-model="user.name" /></td>
        <td><input type="text" v-model="user.username" /></td>
        <td><input type="text" v-model="user.email" /></td>
      </tr>
    </tbody>
  </table>
</template>

<style>
table,
th,
td {
  border: 1px solid black;
  border-collapse: collapse;
  padding: 1em;
}
.dragging {
  background-color: #aaa;
}
</style>