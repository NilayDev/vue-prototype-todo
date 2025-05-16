<template>
  <div class="todo-app">
    <div class="todo-section">
      <h2>Active Tasks ({{ incompleteTodos.length }})</h2>
      <div class="todo-input">
        <input
          type="text"
          placeholder="Add a new task..."
          v-model.trim="newTodo"
          @keyup.enter="addTodo"
        />
      </div>

      <p v-if="!incompleteTodos.length" class="empty-state">No active tasks</p>
      <draggable
        class="todo-list"
        tag="ul"
        :list="incompleteTodos"
        @change="handleDragChange"
        itemKey="id"
        :group="dragGroup"
      >
        <template #item="{ element }">
          <li class="todo-item">
            <input type="checkbox" :name="element.id" v-model="element.completed" />
            <div class="todo-content">
              <div class="todo-text">{{ element.text }}</div>
              <button class="delete-btn" @click="deleteTodo(element)">×</button>
            </div>
          </li>
        </template>
      </draggable>
    </div>

    <div class="completed-section">
      <h2>Completed ({{ completedTodos.length }})</h2>
      <p v-if="!completedTodos.length" class="empty-state">No completed tasks</p>
      <draggable
        class="todo-list completed"
        tag="ul"
        :list="completedTodos"
        @change="handleDragChange"
        itemKey="id"
        :group="dragGroup"
      >
        <template #item="{ element }">
          <li class="todo-item">
            <input :name="element.id" type="checkbox" v-model="element.completed" />
            <div class="todo-content">
              <div class="todo-text">{{ element.text }}</div>
              <button class="delete-btn" @click="deleteTodo(element)">×</button>
            </div>
          </li>
        </template>
      </draggable>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable'

export default {
  name: 'TodoApp',
  components: { draggable },

  data() {
    return {
      newTodo: '',
      dragGroup: { name: 'todos' },
      todos: [
        { id: Date.now() - 10, text: 'Send job application', completed: true },
        { id: Date.now() - 20, text: 'Learn about Vue', completed: false },
        { id: Date.now() - 30, text: 'Learn about Fliplet', completed: false },
        { id: Date.now() - 40, text: 'Play around in JSFiddle', completed: false },
        { id: Date.now() - 50, text: 'Build something awesome', completed: false },
      ],
    }
  },

  computed: {
    completedTodos() {
      return this.todos.filter((todo) => todo.completed)
    },
    incompleteTodos() {
      return this.todos.filter((todo) => !todo.completed)
    },
  },

  methods: {
    addTodo() {
      const text = this.newTodo.trim()
      if (!text) return

      this.todos.push({
        id: Date.now(),
        text,
        completed: false,
      })
      this.newTodo = ''
    },

    deleteTodo(todo) {
      const index = this.todos.findIndex((el) => el.id === todo.id)
      if (index > -1) {
        this.todos.splice(index, 1)
      }
    },

    handleDragChange(event) {
      const { added, moved } = event

      if (!added && !moved) return

      if (added) {
        added.element.completed = !added.element.completed
      }

      this.todos = [...this.incompleteTodos, ...this.completedTodos]
    },
  },
}
</script>

<style scoped>
.todo-app {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family:
    system-ui,
    -apple-system,
    sans-serif;
}

.todo-section,
.completed-section {
  margin-bottom: 30px;
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

h2 {
  color: #333;
  margin-bottom: 20px;
  font-size: 1.4rem;
  font-weight: 600;
}

.todo-input {
  margin-bottom: 25px;
}

.todo-input input {
  width: 93%;
  padding: 10px 15px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 15px;
  transition: border-color 0.2s;
  background: #f8f9fa;
}

.todo-input input:focus {
  outline: none;
  border-color: #4caf50;
}

.todo-list {
  list-style: none;
  padding: 0;
  min-height: 30px;
}

.todo-item {
  padding: 10px 15px;
  background: #f8f9fa;
  margin-bottom: 8px;
  border-radius: 6px;
  display: flex;
  align-items: center;
  border: 1px solid #eee;
}

.todo-item:hover {
  box-shadow: 0 5px 7px rgba(0, 0, 0, 0.05);
  cursor: all-scroll;
}

.todo-item input[type='checkbox'] {
  width: 16px;
  height: 16px;
  border-radius: 3px;
  cursor: pointer;
}

.todo-text {
  flex: 1;
  font-size: 14px;
  color: #333;
}

.delete-btn {
  background: none;
  border: none;
  color: #ff4444;
  cursor: pointer;
  font-size: 18px;
  padding: 0 6px;
  opacity: 0.6;
}

.delete-btn:hover {
  opacity: 1;
}

.empty-state {
  color: #666;
  font-style: italic;
  text-align: center;
  padding: 15px;
  background: #f8f9fa;
  border-radius: 6px;
  border: 1px dashed #ddd;
}

.completed .todo-text {
  text-decoration: line-through;
  color: #999;
}

.completed .todo-item {
  background: #f5f5f5;
  opacity: 0.9;
}

.todo-content {
  display: flex;
  justify-content: space-between;
  width: 100%;
  margin-left: 10px;
}
</style>
