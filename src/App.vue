<script>
import serverTodos from './data/todos.js'
import StatusFilter from './StatusFilter.vue'
import TodoItem from './TodoItem.vue'
export default {
  components: {
    StatusFilter,
    TodoItem,
  },
  data() {
    const todos = JSON.parse(localStorage.getItem('todos')) || serverTodos;
    console.log('todos', todos);

    return {
      todos,
      title: '',
      status: 'all',
    }
  },
  watch: {
    todos: {
      deep: true,
      handler() {
        localStorage.setItem('todos', JSON.stringify(this.todos))
      },
    },
  },
  computed: {
    activeTodos() {
      return this.todos.filter(({ completed }) => !completed)
    },
    completedTodos() {
      return this.todos.filter(({ completed }) => completed)
    },
    visibleTodos() {
      switch (this.status) {
        case 'active':
          return this.activeTodos;
        case 'completed':
          return this.completedTodos;
        default:
          return this.todos;
      }
    },
  },
  methods: {
    handleSubmit() {
      this.todos.push({
        id: Date.now(),
        title: this.title,
        completed: false,
      })
    },
    filterTodos(currentFilter) {
      this.todos = this.todos.filter(
        ({ completed }) => completed === currentFilter,
      )
    },
  },
}
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <button
          type="button"
          class="todoapp__toggle-all"
          :class="{ active: !activeTodos.length && !!completedTodos.length }"
        ></button>

        <form @submit.prevent="handleSubmit">
          <input
            type="text"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            v-model="title"
          />
        </form>
      </header>

      <TransitionGroup name="list" tag="section" class="todoapp__main">
        <TodoItem
          v-for="todo of visibleTodos"
          :key="todo.id"
          :todo="todo"
          @update="Object.assign(todo, $event)"
          @delete="todos.splice(todos.indexOf(todo), 1)"
        />
      </TransitionGroup>

      <footer class="todoapp__footer">
        <span class="todo-count"> {{ activeTodos.length }} items left </span>

        <StatusFilter v-model:value="status" />

        <button
          type="button"
          class="todoapp__clear-completed"
          v-if="completedTodos.length > 0"
        >
          Clear completed
        </button>
      </footer>
    </div>
  </div>
</template>

<style>
.list-enter-active,
.list-leave-active {
  height: 60px;
  transition: all 1s ease;
}

.list-enter-from,
.list-leave-to{
  height: 0;
  opacity: 0;
  transform: scaleY(0);
  transform-origin: top;
}
</style>
