<script setup>
import { computed, ref, watch } from "vue";
import { uid } from "uid";
import { Icon } from "@iconify/vue";
import TodoCreator from "../components/TodoCreator.vue";
import TodoItem from "../components/TodoItem.vue";

const todoList = ref([]);

watch(
  todoList,
  () => {
    saveTodoToLocalStorage();
  },
  {
    deep: true,
  }
);

const fetchTodoListFromLocalStorage = () => {
  const todoListFromLocalStorage = localStorage.getItem("todoList");
  if (todoListFromLocalStorage) {
    todoList.value = JSON.parse(todoListFromLocalStorage);
  }
};

fetchTodoListFromLocalStorage();

const saveTodoToLocalStorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value));
};

const createTodo = (todo) => {
  console.log(todo);
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: null,
    isEditing: false,
  });
};

const toggleTodoCompleted = (index) => {
  todoList.value[index].isCompleted = !todoList.value[index].isCompleted;
};

const toggleTodoEditing = (index) => {
  todoList.value[index].isEditing = !todoList.value[index].isEditing;
};

const updateTodo = (todo, index) => {
  console.log(todo, index);
  todoList.value[index].todo = todo;
  todoList.value[index].isEditing = false;
};

const deleteTodo = (todoId) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== todoId);
};

const todosCompleted = computed(() => {
  return todoList.value.every((todo) => todo.isCompleted);
});
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem
        v-for="(todo, index) in todoList"
        :todo="todo"
        :index="index"
        :key="todo.id"
        @todo-completed="toggleTodoCompleted"
        @edit-todo="toggleTodoEditing"
        @update-todo="updateTodo"
        @delete-todo="deleteTodo"
      />
    </ul>
    <p v-else class="todos-msg">
      <Icon icon="noto-v1:sad-but-relieved-face" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p v-if="todosCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" />
      <span>You have completed all your todos!</span>
    </p>
  </main>
</template>

<style lang="scss" scoped>
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>
