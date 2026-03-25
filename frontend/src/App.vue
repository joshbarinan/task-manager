<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const API = "http://127.0.0.1:8000/tasks/";

const tasks = ref([]);
const title = ref("");
const description = ref("");
const loading = ref(false);
const error = ref("");

const fetchTasks = async () => {
  try {
    loading.value = true;
    const res = await axios.get(API);
    tasks.value = res.data;
  } catch (err) {
    error.value = "Failed to load tasks";
  } finally {
    loading.value = false;
  }
};

onMounted(fetchTasks);

const createTask = async () => {
  if (!title.value.trim()) {
    alert("Title required");
    return;
  }

  await axios.post(API, {
    title: title.value,
    description: description.value,
  });

  title.value = "";
  description.value = "";
  fetchTasks();
};

const deleteTask = async (id) => {
  await axios.delete(`${API}${id}/`);
  fetchTasks();
};

const toggleTask = async (id) => {
  await axios.patch(`${API}${id}/`);
  fetchTasks();
};

const updateTask = async (task) => {
  const newTitle = prompt("New title:", task.title);
  const newDesc = prompt("New description:", task.description);

  if (!newTitle) return;

  await axios.put(`${API}${task.id}/`, {
    title: newTitle,
    description: newDesc,
  });

  fetchTasks();
};
</script>

<template>
  <div style="padding: 20px">
    <h2>Task Manager</h2>

    <p v-if="loading">Loading...</p>
    <p v-if="error" style="color:red">{{ error }}</p>

    <input v-model="title" placeholder="Title" />
    <input v-model="description" placeholder="Description" />
    <button @click="createTask">Add</button>

    <ul>
      <li v-for="task in tasks" :key="task.id">
        <b>{{ task.title }}</b> - {{ task.description }}
        | {{ task.completed ? "✅" : "❌" }}

        <button @click="toggleTask(task.id)">Toggle</button>
        <button @click="updateTask(task)">Edit</button>
        <button @click="deleteTask(task.id)">Delete</button>
      </li>
    </ul>
  </div>
</template>