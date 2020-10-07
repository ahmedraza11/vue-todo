<template>
  <div class="todo-item">
    <div class="todo-item-left">
      <input type="checkbox" v-model="completed" @change="doneTodo" />
      <div
        v-if="!editing"
        class="todo-item-label"
        @dblclick="editTodo"
        :class="{ completed: completed }"
      >
        {{ title }}
      </div>
      <input
        v-else
        class="todo-item-edit"
        type="text"
        v-focus
        v-model="title"
        @blur="doneTodo"
        @keyup.enter="doneTodo"
        @keyup.esc="cancelTodo"
      />
    </div>
    <div @click="removeTodo(index)" class="remove-item">&times;</div>
  </div>
</template>

<script>
export default {
  name: "todo-item",
  props: {
    todo: {
      type: Object,
      required: true,
    },
    index: {
      type: Number,
      required: true,
    },
    checkAll: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      id: this.todo.id,
      title: this.todo.title,
      completed: this.todo.completed,
      editing: this.todo.editing,
      beforeEditing: "",
    };
  },
  watch: {
    checkAll() {
      if (this.checkAll) {
        this.completed = true;
      } else {
        this.completed = this.todo.completed;
      }
    },
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus();
      },
    },
  },
  methods: {
    removeTodo(index) {
      this.$emit("removedTodo", index);
    },
    doneTodo() {
      if (this.title.trim() == 0) {
        this.title = this.beforeEditing;
      }
      this.editing = false;
      this.$emit("finishedTodo", {
        index: this.id,
        todo: {
          id: this.id,
          title: this.title,
          completed: this.completed,
          editing: this.editing,
        },
      });
    },
    editTodo() {
      this.beforeEditing = this.title;
      this.editing = true;
    },
    cancelTodo() {
      this.title = this.beforeEditing;
      this.editing = false;
    },
  },
};
</script>