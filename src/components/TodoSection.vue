<template>
  <section class="todo-section">
    <div class="h1">{{ title }}</div>

    <form v-if="this.type === 'active'" class="todo-section-add">
      <input
        v-model="newTodo"
        class="todo-section-add-input"
        placeholder="Add new task..."
      />
      <button class="btn btn-purple" @click.prevent="addTodo">
        <i class="icon-plus"></i>
      </button>
    </form>

    <div class="todo-list">
      <TodoItem
        v-for="todo in todoList"
        :key="todo.id"
        :todo="todo"
        :type="this.type"
        @remove-todo="removeTodo"
        ref="todoItems"
      />
      <transition apppar @enter="noTodosAnimation">
        <h3 v-if="!todoList.length" class="todo-list-no-todos">
          {{
            this.type === 'active'
              ? 'No todos found'
              : 'No completed todos found'
          }}
        </h3>
      </transition>
    </div>

    <div v-if="this.type === 'active'" class="todo-list-footer">
      <div class="todo-list-footer-left-text">
        You have {{ todoList.length }} pending tasks
      </div>

      <button class="btn btn-purple" @click="removeAll">Clear all</button>
    </div>
  </section>
</template>

<script>
import { gsap } from 'gsap';
import TodoItem from './TodoItem.vue';

export default {
  data() {
    return {
      newTodo: '',
    };
  },

  props: {
    todoList: {
      type: Array,
      required: true,
    },

    title: {
      type: String,
      required: true,
    },

    type: {
      type: String,
      required: true,
    },
  },

  components: { TodoItem },

  methods: {
    addTodo() {
      this.$emit('add-todo', this.newTodo);

      this.newTodo = '';
    },

    removeTodo(id) {
      this.$emit('remove-todo', id);
    },

    removeAll() {
      const animationPromises = [];

      for (const todoItem of this.$refs.todoItems) {
        const animationPromise = new Promise((resolve) => {
          todoItem.itemOnUnmount(todoItem.$el, resolve);
        });
        animationPromises.push(animationPromise);
      }

      Promise.all(animationPromises).then(() => {
        this.$emit('remove-all');
      });
    },

    noTodosAnimation(el) {
      gsap.from(el, {
        opacity: 0,
        duration: 1,
        ease: 'power1.inOut',
      });
    },
  },
};
</script>
