<script setup>
import { ref, computed } from 'vue'

const tasks = ref([])
const newTask = ref('')
const newDeadline = ref('')
const filterStatus = ref('all')
const showModal = ref(false)

const addTask = () => {
  const text = newTask.value.trim()
  if (text && newDeadline.value) {
    tasks.value.push({
      text,
      completed: false,
      deadline: newDeadline.value,
    })
    newTask.value = ''
    newDeadline.value = ''
    showModal.value = false
  }
}

const deleteTask = (index) => {
  tasks.value.splice(index, 1)
}

const isOverdue = (task) => {
  return (
    !task.completed &&
    new Date(task.deadline) < new Date(new Date().toDateString())
  )
}

const updateDeadline = (task) => {
  task.deadline = task.deadline
}

const filteredTasks = computed(() => {
  if (filterStatus.value === 'all') return tasks.value
  if (filterStatus.value === 'completed') return tasks.value.filter(t => t.completed)
  if (filterStatus.value === 'pending') return tasks.value.filter(t => !t.completed && !isOverdue(t))
  if (filterStatus.value === 'overdue') return tasks.value.filter(t => isOverdue(t))
  return tasks.value
})

const totalTasks = computed(() => tasks.value.length)
const completedTasks = computed(() => tasks.value.filter(t => t.completed).length)
const pendingTasks = computed(() => tasks.value.filter(t => !t.completed).length)
</script>

<template>
  <div class="app">
    <h1>📝 To-Do List</h1>

    <div class="task-summary">
      <p>Total Tugas: {{ totalTasks }} | Selesai: {{ completedTasks }} | Belum Selesai: {{ pendingTasks }}</p>
    </div>

    <button class="open-modal" @click="showModal = true">➕ Tambah Tugas</button>

    <div class="modal" v-if="showModal">
      <div class="modal-content">
        <h2>Tambah Tugas</h2>

        <div class="input-group">
          <label>Nama Tugas</label>
          <input v-model="newTask" type="text" required />
        </div>

        <div class="input-group">
          <label>Tenggat Waktu</label>
          <input type="date" v-model="newDeadline" required />
        </div>

        <div class="modal-buttons">
          <button @click="addTask">Tambah</button>
          <button class="close" @click="showModal = false">❌ Tutup</button>
        </div>
      </div>
    </div>

    <div class="filter">
      <label>Filter:</label>
      <select v-model="filterStatus">
        <option value="all">Semua</option>
        <option value="completed">Selesai</option>
        <option value="pending">Belum</option>
        <option value="overdue">Terlambat</option>
      </select>
    </div>

    <ul class="task-list">
      <li
        v-for="(task, index) in filteredTasks"
        :key="index"
        :class="{ done: task.completed, overdue: isOverdue(task) }"
      >
        <input type="checkbox" v-model="task.completed" />
        <span>{{ task.text }}</span>
        <input
          type="date"
          v-model="task.deadline"
          @change="updateDeadline(task)"
          class="edit-deadline"
        />
        <span v-if="isOverdue(task) && !task.completed" class="late">⚠ Terlambat</span>
        <button @click="deleteTask(index)">❌</button>
      </li>
    </ul>
  </div>
</template>

<style>
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
  background-color: #221f1f;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  box-sizing: border-box;
}

*, *::before, *::after {
  box-sizing: inherit;
}

body {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  overflow: hidden;
  color: white;
  background-color: #302f2f;
  font-weight: 500;
  line-height: 1.6;
  letter-spacing: 0.02em;
}

.app {
  width: calc(100vw - 60px);
  height: 100vh;
  background-color: transparent;
  padding: 30px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

h1 {
  text-align: center;
  color: #2d8cf0;
  margin-bottom: 20px;
}

.task-summary {
  text-align: center;
  color: #ccc;
  margin-bottom: 20px;
}

.open-modal {
  display: block;
  margin: 0 auto 20px;
  background-color: #2d8cf0;
  color: white;
  border: none;
  padding: 12px 20px;
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
}

.open-modal:hover {
  background-color: #1a6fc3;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.75);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 999;
}

.modal-content {
  background: #222;
  padding: 25px;
  border-radius: 12px;
  width: 90%;
  max-width: 400px;
  color: #fff;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.7);
}

.modal-content h2 {
  margin-top: 0;
  text-align: center;
  margin-bottom: 20px;
}

.input-group {
  margin-bottom: 20px;
}

.input-group label {
  display: block;
  margin-bottom: 6px;
  font-weight: bold;
  color: #ccc;
}

.input-group input {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid #555;
  background: #2a2a2a;
  color: #fff;
  border-radius: 6px;
  outline: none;
  font-size: 15px;
}

.modal-buttons {
  display: flex;
  justify-content: space-between;
}

.modal-buttons button {
  padding: 10px 16px;
  border: none;
  border-radius: 6px;
  font-weight: bold;
  cursor: pointer;
}

.modal-buttons button:first-child {
  background-color: #2d8cf0;
  color: white;
}

.modal-buttons .close {
  background-color: #444;
  color: #ccc;
}

.modal-buttons .close:hover {
  background-color: #666;
}

.filter {
  margin-bottom: 15px;
  display: flex;
  align-items: center;
  gap: 10px;
}

.filter select {
  background-color: #2a2a2a;
  border: 1px solid #444;
  padding: 8px 12px;
  border-radius: 8px;
  color: #f5f5f5;
}

.task-list {
  list-style: none;
  padding: 0;
  max-height: 60vh;
  overflow-y: auto;
}

.task-list li {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 12px;
  background-color: #292929;
  padding: 12px 10px;
  margin-bottom: 10px;
  border-radius: 10px;
  transition: background 0.3s;
}

.task-list li.done {
  background-color: #1d3c1d;
}

.task-list li.overdue {
  background-color: #4a1f1f;
}

.task-list li span {
  flex: 1 1 auto;
  font-size: 16px;
}

.task-list li.done span {
  text-decoration: line-through;
  color: #9ed69e;
}

.edit-deadline {
  padding: 6px;
  border-radius: 6px;
  border: 1px solid #444;
  background-color: #2a2a2a;
  color: #f5f5f5;
  font-size: 14px;
}

.task-list .late {
  color: #ff6b6b;
  font-weight: bold;
  font-size: 0.85em;
}

.task-list button {
  margin-left: auto;
  background: none;
  border: none;
  color: #bbb;
  font-size: 18px;
  cursor: pointer;
}

.task-list button:hover {
  color: #ff4d4d;
}
</style>
