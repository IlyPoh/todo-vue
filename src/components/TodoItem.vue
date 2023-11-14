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
import { gsap } from 'gsap';

export default {
  data() {
    return {
      isVisible: true,
    };
  },
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

  mounted() {
    this.itemOnMountAnimation(this.$el);
  },

  methods: {
    toggleTodo() {
      this.itemOnUnmount(this.$el, () => {
        this.todo.completed = !this.todo.completed;
      });
    },

    itemClasses(completeStatus) {
      return completeStatus ? 'completed' : null;
    },

    removeTodo() {
      const id = this.todo.id;
      this.itemOnUnmount(this.$el, () => {
        this.$emit('remove-todo', id);

        this.isVisible = false;
      });
    },

    // animations
    itemOnMountAnimation(el) {
      gsap.from(el, {
        scale: 0,
        duration: 1,
        ease: 'power1.inOut',
      });
    },

    itemOnUnmount(el, onCompleteCallback) {
      gsap.to(el, {
        scale: 0,
        height: 0,
        duration: 1,
        ease: 'power1.inOut',
        onComplete: onCompleteCallback,
      });
    },
  },
};
</script>
