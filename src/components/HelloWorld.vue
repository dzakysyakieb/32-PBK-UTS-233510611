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

