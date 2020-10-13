<template>
  <div id="app">
    <todo-header></todo-header>
    <todo-table></todo-table>
  </div>
</template>

<script>
import TodoTable from "@/components/TodoTable";
import TodoHeader from "@/components/TodoHeader";

export default {
  name: "app",
  components: {
    TodoTable,
    TodoHeader
  }
};
</script>
