<template>
  <section class="todo">
    <TodoSection
      :todoList="filters.active"
      title="Your todo list"
      type="active"
      @add-Todo="addTodo"
      @remove-all="removeAll"
      @remove-todo="removeTodo"
    />

    <TodoSection
      :todoList="filters.completed"
      title="Completed todos"
      type="completed"
    />
  </section>
</template>
<script>
import TodoSection from './TodoSection.vue';

export default {
  data() {
    return {
      todos: [],
      filteredTodos: [],
    };
  },

  components: {
    TodoSection,
  },

  async mounted() {
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

  computed: {
    filters() {
      return {
        active: this.todos.filter((todo) => !todo.completed),
        completed: this.todos.filter((todo) => todo.completed),
      };
    },
  },

  methods: {
    addTodo(name) {
      if (name.length) {
        this.todos.push({
          id: Date.now(),
          title: name,
          completed: false,
        });
      }
    },

    removeTodo(id) {
      this.todos = this.todos.filter((todo) => todo.id !== id);
    },

    removeAll() {
      this.todos = [];
    },
  },
};
</script>
