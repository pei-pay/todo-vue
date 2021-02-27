<template>
<div>
  <input type="text" class="todo__input" placeholder="What needs to be done" v-model="newTodo" @keyup.enter="addTodo">
  <transition-group
      enter-active-class="animate__animated animate__fadeInLeft"
      leave-active-class="animate__animated animate__fadeOutRight"
  >
    <todo-item v-for="todo in todosFiltered" :key="todo.id" 
        :title="todo.title" :completed="todo.completed"
        @on-delete="removeTodo(todo)" @on-edit="editTodo(todo, $event)"
        @on-toggle="toggleTodo(todo)"
    />
  </transition-group>

  <div class="extra-container">
    <div><label><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos">Check All</label></div>
    <div>{{ remaining }} items left</div>
  </div>

  <div class="extra-container">
    <div>
      <button :class="{ active: filter == 'all'}" @click="filter = 'all'">All</button>
      <button :class="{ active: filter == 'ongoing'}" @click="filter = 'ongoing'">Ongoing</button>
      <button :class="{ active: filter == 'completed'}" @click="filter = 'completed'">Completed</button>
    </div>

    <div>
      <transition
          enter-active-class="animate__animated animate__fadeInUp" leave-active-class="animate__animated animate__fadeOutDown"
      >
        <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
      </transition>
    </div>
  </div>
</div>
</template>

<script>
import TodoItem from './TodoItem.vue'

export default {
  name: 'TodoList',
  components: { 
    TodoItem, 
  },
  data() {
    return {
      newTodo: '',
      idForTodo: 4,
      beforeEditCache: '',
      filter: 'all',
      todos: [
        {
          'id': 1,
          'title': 'Finish Vue Project',
          'completed': false,
        },
        {
          'id': 2,
          'title': 'Learn Nuxt.js',
          'completed': false,
        },
        {
          'id': 3,
          'title': 'Use GitHub',
          'completed': false,
        },
      ]
    }
  },
  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length
    },
    anyRemaining() {
      return this.remaining != 0
    },
    todosFiltered() {
      if(this.filter == 'ongoing') {
        return this.todos.filter(todo => !todo.completed)
      } else if(this.filter == 'completed') {
        return this.todos.filter(todo => todo.completed)
      } else {
        return this.todos
      }
    },
    showClearCompletedButton() {
      return this.todos.filter(todo => todo.completed).length  > 0
    },
  },
  methods: {
    addTodo() {
      if(this.newTodo.trim().length == 0) {
        return
      }

      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
      })

      this.newTodo = ''
      this.idForTodo++
    },
    editTodo(todo, editedTitle) {
      todo.title = editedTitle
    },
    removeTodo(todo) {
      let index = this.todos.indexOf(todo);
			this.todos.splice(index, 1);
    },
    toggleTodo(todo) {
      todo.completed = !todo.completed
    },
    checkAllTodos(event) {
      this.todos.forEach(todo => todo.completed = event.target.checked)
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  }
}
</script>

<style lang="scss">
  @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css");

  .todo {
    &__input {
      width: 100%;
      padding: 10px 18px;
      font-size: 18px;
      margin-bottom: 16px;

      &:focus {
        outline: none;
      }
    }

    &__item {
      margin-bottom: 12px;
      display: flex;
      align-items: center;
      justify-content: space-between;

      &__remove {
        cursor: pointer;
        margin-left: 14px;
        &:hover {
          color: black;
        }
      }

      &__left {
        display: flex;
        align-items: center;
      }

      &__label {
        padding: 10px;
        border: 1px solid white;
        margin-left: 12px;
      }

      &__edit {
        font-size: 24px;
        color: #2c3e50;
        margin-left: 12px;
        width: 100%;
        padding: 10px;
        border: 1px solid #ccc;
        font-family: 'Avenir', Arial, Helvetica, sans-serif;

        &:focus {
          outline: none;
        }
      }
    }
  }

  .completed {
    text-decoration: line-through;
    color: grey;
  }

  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;

    button:not(:first-child) {
      margin-left: 10px;
    }
  }

  button {
    font-size: 14px;
    background-color: white;
    appearance: none;

    &:hover {
      background: lightgreen;
    }

    &:focus {
      outline: none;
    }

  }
  .active {
      background: lightgreen;
  }
</style>