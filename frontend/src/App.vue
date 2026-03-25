<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const API = "http://127.0.0.1:8000/tasks/";

const tasks = ref([]);
const title = ref("");
const description = ref("");
const loading = ref(false);
const error = ref("");

// modal state
const showModal = ref(false);
const editTask = ref(null);
const editTitle = ref("");
const editDescription = ref("");

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

// open modal
const openEditModal = (task) => {
  editTask.value = task;
  editTitle.value = task.title;
  editDescription.value = task.description;
  showModal.value = true;
};

// save edit
const saveEdit = async () => {
  if (!editTitle.value.trim()) return;

  await axios.put(`${API}${editTask.value.id}/`, {
    title: editTitle.value,
    description: editDescription.value,
  });

  showModal.value = false;
  fetchTasks();
};
</script>

<template>
  <div class="container">
    <h2>Task Manager</h2>

    <p v-if="loading">Loading...</p>
    <p v-if="error" class="error">{{ error }}</p>

    <!-- Create -->
    <div class="card">
      <input v-model="title" placeholder="Title" />
      <input v-model="description" placeholder="Description" />
      <button class="primary" @click="createTask">Add Task</button>
    </div>

    <!-- List -->
    <div v-for="task in tasks" :key="task.id" class="task">
      <div>
        <b>{{ task.title }}</b>
        <p>{{ task.description }}</p>
      </div>

      <div class="actions">
        <span class="status">{{ task.completed ? "✅" : "❌" }}</span>
        <button class="toggle" @click="toggleTask(task.id)">Toggle</button>
        <button class="edit" @click="openEditModal(task)">Edit</button>
        <button class="danger" @click="deleteTask(task.id)">Delete</button>
      </div>
    </div>

    <!-- MODAL -->
    <div v-if="showModal" class="modal">
      <div class="modal-content">
        <h3>Edit Task</h3>

        <input v-model="editTitle" placeholder="Title" />
        <input v-model="editDescription" placeholder="Description" />

        <div class="modal-actions">
          <button class="primary" @click="saveEdit">Save</button>
          <button @click="showModal = false">Cancel</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
body {
  margin: 0;
  background: #f4f6f8;
}

.container {
  max-width: 700px;
  margin: 40px auto;
  font-family: Arial, sans-serif;
}

h2 {
  text-align: center;
  color: #333;
}

.card {
  background: white;
  padding: 15px;
  margin-bottom: 20px;
  border-radius: 10px;
  display: flex;
  gap: 10px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.05);
}

input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 6px;
}

.task {
  background: white;
  border-radius: 10px;
  padding: 15px;
  margin-bottom: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 0 2px 8px rgba(0,0,0,0.05);
}

.task b {
  font-size: 16px;
  color: #333;
}

.task p {
  margin: 5px 0 0;
  color: #666;
}

.actions {
  display: flex;
  align-items: center;
  gap: 8px;
}

button {
  border: none;
  border-radius: 6px;
  padding: 6px 12px;
  cursor: pointer;
  font-size: 14px;
}

/* Buttons */
.primary {
  background: #4caf50;
  color: white;
}

.primary:hover {
  background: #43a047;
}

.toggle {
  background: #e0e0e0;
}

.toggle:hover {
  background: #d5d5d5;
}

.edit {
  background: #2196f3;
  color: white;
}

.edit:hover {
  background: #1e88e5;
}

.danger {
  background: #f44336;
  color: white;
}

.danger:hover {
  background: #e53935;
}

.status {
  font-size: 18px;
}

/* modal */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.4);

  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background: white;
  padding: 20px;
  border-radius: 10px;
  width: 320px;
}

.modal-actions {
  margin-top: 15px;
  display: flex;
  justify-content: space-between;
}
</style>