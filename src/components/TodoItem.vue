<template>
  <div class="todo-list-item" :class="itemClasses(todo.completed)">
    <div class="todo-list-item-text">
      {{ todo.title }}
    </div>

    <div v-if="type === 'active'" class="todo-list-item-buttons">
      <button class="btn btn-green" @click="toggleTodo" title="Complete task">
        <i class="icon-check"></i>
      </button>

      <button class="btn btn-red" title="Remove task" @click="removeTodo">
        <i class="icon-trash"></i>
      </button>
    </div>

    <div v-if="type === 'completed'" class="todo-list-item-buttons">
      <button
        v-if="type === 'completed'"
        @click="toggleTodo"
        class="btn btn-red"
        title="Uncomplete task"
      >
        <i class="icon-close"></i>
      </button>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    todo: {
      type: Object,
      required: true,
    },

    type: {
      type: String,
      required: true,
    },
  },

  methods: {
    toggleTodo() {
      this.todo.completed = !this.todo.completed;
    },

    itemClasses(completeStatus) {
      return completeStatus ? 'completed' : null;
    },

    removeTodo() {
      const id = this.todo.id;
      this.$emit('remove-todo', id);
    },
  },
};
</script>
