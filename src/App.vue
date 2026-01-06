
<script setup>
import { ref, computed, watchEffect, onMounted } from 'vue'

const todos = ref([])
const newTask = ref('')
const filter = ref('all')

onMounted(() => {
  const saved = localStorage.getItem('todos')
  if (saved) todos.value = JSON.parse(saved)
})

watchEffect(() => {
  localStorage.setItem('todos', JSON.stringify(todos.value))
})

const displayTodos = computed(() => {
  if (filter.value === 'active') return todos.value.filter(t => !t.completed)
  if (filter.value === 'completed') return todos.value.filter(t => t.completed)
  return todos.value
})

const activeTodos = computed(() => todos.value.filter(t => !t.completed).length)
const completedTodos = computed(() => todos.value.filter(t => t.completed).length)

const addTodo = () => {
  if (!newTask.value.trim()) return
  todos.value.unshift({
    id: Date.now(),
    text: newTask.value.trim(),
    completed: false
  })
  newTask.value = ''
}

const toggleTodo = (id) => {
  const todo = todos.value.find(t => t.id === id)
  if (todo) todo.completed = !todo.completed
}

const updateTodo = (id, text) => {
  const todo = todos.value.find(t => t.id === id)
  if (todo && text.trim()) todo.text = text.trim()
}

const deleteTodo = (id) => {
  todos.value = todos.value.filter(t => t.id !== id)
}
</script>

<template>
  <div class="min-vh-100 p-3 p-md-4" style="background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);">
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-12 col-sm-11 col-md-9 col-lg-7 col-xl-6">
          
          <div class="card border-0 shadow-lg rounded-4" style="background: #0f1419;">
            <div class="card-body p-4">
              
              <h1 class="text-white text-center mb-4 fw-bold"> Lista de Tareas by Ronald</h1>
              
              <form @submit.prevent="addTodo" class="mb-4">
                <div class="input-group input-group-lg">
                  <input
                    v-model="newTask"
                    type="text"
                    class="form-control me-3 rounded-3 rounded-end-0 border-secondary"
                    placeholder="Escribe una nueva tarea..."
                    style="background: #1c2530; color: #fff; border-width: 2px;"
                  />
                  <button class="btn btn-primary rounded-3 px-4" type="submit" style="background: #1d9bf0; border: none;">
                    <i class="bi bi-plus-circle-fill"></i> Agregar
                  </button>
                </div>
              </form>

              <div class="d-flex flex-wrap gap-2 mb-4">
                <button
                  @click="filter = 'all'"
                  :class="['btn flex-fill rounded-3', filter === 'all' ? 'btn-light' : 'btn-outline-light']"
                >
                  <i class="bi bi-list-ul"></i> Todas <span class="badge bg-secondary ms-1">{{ todos.length }}</span>
                </button>
                <button
                  @click="filter = 'active'"
                  :class="['btn flex-fill rounded-3', filter === 'active' ? 'btn-success' : 'btn-outline-success']"
                >
                  <i class="bi bi-clock-fill"></i> Activas <span class="badge bg-dark ms-1">{{ activeTodos }}</span>
                </button>
                <button
                  @click="filter = 'completed'"
                  :class="['btn flex-fill rounded-3', filter === 'completed' ? 'btn-secondary' : 'btn-outline-secondary']"
                >
                  <i class="bi bi-check-circle-fill"></i> Completadas <span class="badge bg-dark ms-1">{{ completedTodos }}</span>
                </button>
              </div>

              <div v-if="displayTodos.length > 0" class="d-flex flex-column gap-3">
                <div 
                  v-for="todo in displayTodos" 
                  :key="todo.id"
                  class="card border-0 rounded-3 shadow-sm"
                  style="background: #1c2530;"
                >
                  <div class="card-body d-flex align-items-center gap-3 p-3">
                    <input
                      type="checkbox"
                      :checked="todo.completed"
                      @change="toggleTodo(todo.id)"
                      class="form-check-input m-0"
                      style="width: 24px; height: 24px; cursor: pointer; border: 2px solid #6c757d;"
                    />
                    
                    <input
                      v-model="todo.text"
                      class="form-control form-control-lg border-0 flex-grow-1"
                      :class="todo.completed ? 'text-decoration-line-through text-white' : 'text-white'"
                      style="background: transparent; font-size: 1.1rem;"
                      @blur="updateTodo(todo.id, $event.target.value)"
                      @keyup.enter="$event.target.blur()"
                    />
                    
                    <button
                      @click="deleteTodo(todo.id)"
                      class="btn btn-sm btn-outline-danger rounded-circle"
                      style="width: 40px; height: 40px;"
                      title="Eliminar"
                    >
                      <i class="bi bi-trash-fill"></i>
                    </button>
                  </div>
                </div>
              </div>

              <div v-else class="text-center py-5">
                <i class="bi bi-inbox display-1 text-muted mb-3"></i>
                <p class="text-muted fs-5">
                  {{ filter === 'all' ? '¡No hay tareas! Crea una nueva arriba' : 'No hay tareas en este filtro' }}
                </p>
              </div>

            </div>
          </div>

        </div>
      </div>
    </div>
  </div>
</template>
