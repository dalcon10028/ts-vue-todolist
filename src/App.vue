<template>
  <div>
    <header>
      <h1>Todo List</h1>
    </header>
    <main>
      <TodoInput :item="todoText" @input="updateTodoText" @add="addTodoItem" />
      <div>
        <ul>
          <TodoListItem
            v-for="(todoItem, index) in todoItems"
            :key="index"
            :index="index"
            :todoItem="todoItem"
            @toggle="toggleTodoItemComplete"
            @remove="removeTodoItem"
          />
        </ul>
      </div>
    </main>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import TodoInput from './components/TodoInput.vue';
import TodoListItem from './components/TodoListItem.vue';

const STORAGE_KEY = 'todo-list';

const storage = {
  save(todoItems: Todo[]) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todoItems));
  },
  fetch(): Todo[] {
    const todoItems = localStorage.getItem(STORAGE_KEY) || '[]';
    return JSON.parse(todoItems);
  },
};

export interface Todo {
  title: string;
  done: boolean;
}

export default Vue.extend({
  components: { TodoInput, TodoListItem },
  data() {
    return {
      todoText: '',
      todoItems: [] as Todo[],
    };
  },

  created() {
    this.fetchTodoItems();
  },

  methods: {
    updateTodoText(value: string) {
      this.todoText = value;
    },
    addTodoItem() {
      const value = this.todoText;
      this.todoItems.push({ title: value, done: false });
      storage.save(this.todoItems);
      this.initTodoText();
    },
    initTodoText() {
      this.todoText = '';
    },
    fetchTodoItems() {
      this.todoItems = storage
        .fetch()
        .sort((a, b) => (a.title > b.title ? 1 : -1));
    },
    removeTodoItem(index: number) {
      console.log('remove', index);
      storage.save(this.todoItems.splice(index, 1));
    },
    toggleTodoItemComplete(todoItem: Todo, index: number) {
      this.todoItems.splice(index, 1, {
        ...todoItem,
        done: !todoItem.done,
      });
      storage.save(this.todoItems);
    },
  },
});
</script>
