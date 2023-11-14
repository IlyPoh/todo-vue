<template>
  <transition appear @enter="blockAnimation">
    <section class="todo">
      <transition appear @enter="firstSectionAnimation">
        <TodoSection
          :todoList="filters.active"
          title="Your todo list"
          type="active"
          @add-Todo="addTodo"
          @remove-all="removeAll"
          @remove-todo="removeTodo"
        />
      </transition>

      <transition appear @enter="secondSectionAnimation">
        <TodoSection
          :todoList="filters.completed"
          title="Completed todos"
          type="completed"
        />
      </transition>
    </section>
  </transition>
</template>
<script>
import { gsap } from 'gsap';
import TodoSection from './TodoSection.vue';

export default {
  data() {
    return {
      todos: [],
      completedTodos: [],
    };
  },

  components: {
    TodoSection,
  },

  mounted() {
    this.getTodos();
  },

  computed: {
    filters() {
      return {
        active: this.todos.filter((todo) => !todo.completed),
        completed: this.todos.filter((todo) => todo.completed),
      };
    },
  },

  methods: {
    addTodo(text) {
      if (text.length) {
        const newTodo = {
          id: Date.now(),
          title: text,
          completed: false,
        };

        this.todos = [...this.todos, newTodo];
      }
    },

    async getTodos() {
      try {
        const response = await fetch('/db/todo.json');

        if (response.ok) {
          const data = await response.json();
          this.todos = data;
        } else {
          console.error('Error loading todos:', response.status);
        }
      } catch (error) {
        console.error('Error loading todos:', error);
      }
    },

    removeTodo(id) {
      this.todos = this.todos.filter((todo) => todo.id !== id);
    },

    removeAll() {
      this.todos = this.filters.completed;
    },

    // animations
    blockAnimation(el) {
      gsap.from(el, {
        yPercent: -100,
        opacity: 0,
        duration: 2,
        ease: 'power1.inOut',
      });
    },

    firstSectionAnimation(el) {
      gsap.from(el, {
        xPercent: 100,
        x: '3rem',
        rotateY: 180,
        duration: 3,
        delay: 2,
        ease: 'power1.in',
      });

      gsap.from(el, {
        opacity: 0,
        delay: 4,
        duration: 2,
        ease: 'power1.inOut',
      });
    },

    secondSectionAnimation(el) {
      gsap.from(el, {
        opacity: 0,
        delay: 3.5,
        duration: 2,
        ease: 'power1.inOut',
      });
    },
  },
};
</script>
