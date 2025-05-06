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

