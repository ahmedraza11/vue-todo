<template>
  <div>
    <input
      type="text"
      placeholder="What needs to be done!"
      class="todo-input"
      v-model="newTodo"
      @keyup.enter="addTodo"
    />
    <transition-group
      enter-active-class="animated fadeInUp"
      leave-active-class="animated fadeOutDown"
      name="fade"
    >
      <div
        v-for="(todo, index) in todosFiltered"
        :key="todo.id"
        class="todo-item"
      >
        <div class="todo-item-left">
          <input type="checkbox" v-model="todo.completed" />
          <div
            v-if="!todo.editing"
            class="todo-item-label"
            @dblclick="editTodo(todo)"
            :class="{ completed: todo.completed }"
          >
            {{ todo.title }}
          </div>
          <input
            v-else
            class="todo-item-edit"
            type="text"
            v-focus
            v-model="todo.title"
            @blur="doneTodo(todo)"
            @keyup.enter="doneTodo(todo)"
            @keyup.esc="cancelTodo(todo)"
          />
        </div>
        <div @click="removeTodo(index)" class="remove-item">&times;</div>
      </div>
    </transition-group>
    <div class="extra-container">
      <div>
        <label
          ><input
            type="checkbox"
            :checked="!anyRemaining"
            @change="checkAllTodos"
          />Check All</label
        >
      </div>
      <div>{{ remaining }} items left</div>
    </div>
    <div class="extra-container">
      <div>
        <button :class="{ active: filter == 'all' }" @click="filter = 'all'">
          All
        </button>
        <button
          :class="{ active: filter == 'active' }"
          @click="filter = 'active'"
        >
          Active
        </button>
        <button
          :class="{ active: filter == 'completed' }"
          @click="filter = 'completed'"
        >
          Completed
        </button>
      </div>
      <div>
        <transition name="fade">
          <button v-if="showClearCompleteBtn" @click="clearCompletedTodo">
            Clear Completed
          </button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "TodoList",
  data() {
    return {
      newTodo: "",
      todoId: 3,
      beforeEditing: "",
      filter: "all",
      todos: [
        {
          id: 1,
          title: "Hello I am first todo",
          completed: false,
          editing: false,
        },
        {
          id: 2,
          title: "Hello I am second todo",
          completed: false,
          editing: false,
        },
      ],
    };
  },
  computed: {
    remaining() {
      return this.todos.filter((todo) => !todo.completed).length;
    },
    anyRemaining() {
      return this.remaining !== 0;
    },
    todosFiltered() {
      if (this.filter == "all") {
        return this.todos;
      } else if (this.filter == "active") {
        return this.todos.filter((todo) => !todo.completed);
      } else if (this.filter == "completed") {
        return this.todos.filter((todo) => todo.completed);
      } else {
        return this.todos;
      }
    },
    showClearCompleteBtn() {
      return this.todos.filter((todo) => todo.completed).length;
    },
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim().length == 0) {
        return;
      }
      this.todos.push({
        id: this.todoId,
        title: this.newTodo,
        completed: false,
        editing: false,
      });
      this.newTodo = "";
      this.todoId++;
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    doneTodo(todo) {
      if (todo.title.trim() == 0) {
        todo.title = this.beforeEditing;
      }
      todo.editing = false;
    },
    editTodo(todo) {
      this.beforeEditing = todo.title;
      todo.editing = true;
    },
    cancelTodo(todo) {
      todo.title = this.beforeEditing;
      todo.editing = false;
    },
    checkAllTodos() {
      this.todos.forEach((todo) => (todo.completed = event.target.checked));
    },
    clearCompletedTodo() {
      this.todos = this.todos.filter((todo) => !todo.completed);
    },
  },
  directives: {
    focus: {
      inserted: function(el) {
        el.focus();
      },
    },
  },
};
</script>

<style lang="scss">
@import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");
.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;
  &:focus {
    outline: 0;
  }
}
.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  animation-duration: 0.4s;
}
.remove-item {
  cursor: pointer;
  margin-left: 14px;
  &:hover {
    color: black;
  }
}
.todo-item-left {
  display: flex;
  align-items: center;
}
.todo-item-label {
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
}
.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: solid 1px #ccc;
  font-family: Avenir, Helvetica, Arial, sans-serif;
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
  border-top: 1px solid lightgray;
  padding-top: 14px;
  margin-bottom: 14px;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
.active {
  background: lightgreen;
}
</style>
