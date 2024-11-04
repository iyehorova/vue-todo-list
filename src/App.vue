<script>
import { createTodo, deleteTodo, getTodos, updateTodo } from './API/todos.js'
import ErrorMessage from './components/MessageBlock.vue';
import StatusFilter from './components/StatusFilter.vue'
import TodoItem from './components/TodoItem.vue'
export default {
  components: {
    StatusFilter,
    TodoItem,
    ErrorMessage,
  },
  data() {
    return {
      todos: [],
      title: '',
      status: 'all',
      errorMessage: '',
    }
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
    isActiveToggleBtn() {
      return !this.activeTodos.length && !!this.completedTodos.length
    }
  },
  mounted() {
    getTodos()
      .then(({ data }) => {
        this.todos = data;
        console.log('everything is fine');
      })
      .catch(() => {
        console.log('error')
        this.errorMessage = 'Unable load todos';
      })
  },
  methods: {
    handleSubmit() {
      createTodo(this.title).then(({ data }) => {
        this.todos.push(data);
        this.title = '';
      })
    },
    updateTodo({id, title, completed }) {
      updateTodo({ id, title, completed }).then(({ data }) => {
        this.todos = this.todos.map((todo) => todo.id !== id? todo : data )
      })
    },
    deleteTodo(todoId) {
      deleteTodo(todoId).then(() => {
        this.todos = this.todos.filter(({id}) => id !== todoId)
      })
    },
    clearCompleted() {
      const completedIds = this.completedTodos.map(todo => this.deleteTodo(todo.id));
      Promise.all(completedIds)
        .then(() => this.todos = this.activeTodos)
        .catch(() => this.errorMessage = "Unable delete completed todos");
    },
    toggleCompleted() {
      if (!this.completedTodos.length) {
        return;
      }
      const updatedTodos = this.completedTodos.map(todo => ({ ...todo, completed: !todo.completed }));
      console.log('updatedTodos', updatedTodos);
      const promisesMap = updatedTodos.map(todo => this.updateTodo(todo));
      Promise.all(promisesMap)
        .then(() => this.todos = updatedTodos)
        .catch(() => this.errorMessage = 'Unable to change status')
    }
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
          :class="{ active: isActiveToggleBtn }"
          @click="toggleCompleted"
          :disabled="!isActiveToggleBtn"
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
          @update="updateTodo"
          @delete="deleteTodo(todo.id)"
        />
      </TransitionGroup>

      <footer class="todoapp__footer">
        <span class="todo-count"> {{ activeTodos.length }} items left </span>

        <StatusFilter v-model:value="status" />

        <button
          type="button"
          class="todoapp__clear-completed"
          v-if="completedTodos.length > 0"
          v-on:click="clearCompleted"
        >
          Clear completed
        </button>
      </footer>
    </div>

    <ErrorMessage
      class="is-danger"
      :active="errorMessage !== ''"
      @hide="errorMessage = ''"
    >
      <p>{{ errorMessage }}</p>
      <template #header><p>Error</p></template>
    </ErrorMessage>
  </div>
</template>

<style>
.list-enter-active,
.list-leave-active {
  transform: scaleY(1);
  height: 58px;
  transition:
    opacity 0.5s 0.2s ease-out,
    height 0.5s ease-out;
}

.list-enter-from,
.list-leave-to {
  height: 0;
  opacity: 0;
  transform: scaleY(0);
}
</style>
