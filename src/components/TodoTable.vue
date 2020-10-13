<template>
  <div>
    <section class="section">
      <div class="container">
        <div class="level">
          <b-button class="is-success" @click="isAddModalActive = true">
            Add Todo
          </b-button>
          <b-button class="is-danger" @click="deleteAllTodos">
            Delete All Todos
          </b-button>
        </div>

        <b-table :data="todos" default-sort="priority">
          <template slot-scope="todos">
            <b-table-column field="id" label="ID" width="40" sortable numeric>
              {{ todos.row.id }}
            </b-table-column>

            <b-table-column field="todo" label="Todo" sortable>
              {{ todos.row.todo }}
            </b-table-column>

            <b-table-column field="priority" label="Priority" sortable>
              {{ todos.row.priority }}
            </b-table-column>

            <b-table-column label="Edit">
              <b-button
                type="is-text"
                icon-left="pencil"
                @click="openEditModal(todos.row)"
              ></b-button>
            </b-table-column>

            <b-table-column label="Delete">
              <b-button
                type="is-text"
                icon-left="delete"
                @click="DeleteAction(todos.row)"
              ></b-button>
            </b-table-column>
          </template>
        </b-table>
      </div>
    </section>

    <b-modal :active.sync="isEditModalActive" has-modal-card>
      <todo-edit-modal
        :todo="selectedTodo"
        :priorities="priorities"
        @edit-todo="EditAction"
      ></todo-edit-modal>
    </b-modal>

    <b-modal :active.sync="isAddModalActive" has-modal-card>
      <todo-add-modal
        @add-todo="AddAction"
        :priorities="priorities"
      ></todo-add-modal>
    </b-modal>
  </div>
</template>

<script>
import TodoEditModal from "@/components/TodoEditModal";
import TodoAddModal from "@/components/TodoAddModal";

export default {
  name: "TodoTable",
  components: { TodoEditModal, TodoAddModal },
  data() {
    return {
      initialTodos: [
        {
          id: 1,
          todo: "Test 1",
          priority: "life changing"
        },
        {
          id: 2,
          todo: "Test 2",
          priority: "life changing"
        },
        {
          id: 3,
          todo: "Test 3",
          priority: "important"
        },
        {
          id: 4,
          todo: "Test 4",
          priority: "demo"
        },
        {
          id: 5,
          todo: "Test 5",
          priority: "important"
        }
      ],
      todos: [],
      priorities: [
        { id: 1, name: "life changing" },
        { id: 2, name: "important" },
        { id: 3, name: "demo" }
      ],
      isEditModalActive: false,
      selectedTodo: {},
      isAddModalActive: false
    };
  },
  mounted() {
    if (localStorage.getItem("todos")) {
      this.todos = JSON.parse(localStorage.getItem("todos"));
    } else {
      this.todos = this.initialTodos;
    }
  },
  methods: {
    openEditModal(todo) {
      this.selectedTodo = todo;
      this.isEditModalActive = true;
    },
    AddAction(item) {
      const highestId = Math.max.apply(Math,this.todos.map(item => item.id));
      this.todos.push({
        id: highestId + 1,
        todo: item.title,
        priority: item.priority
      });
      this.saveLocalStorageTodos();
      this.isAddModalActive = false;
    },
    EditAction(item) {
      const todo = this.findTodo(item);
      todo.todo = item.todo;
      todo.priority = item.priority;
      this.saveLocalStorageTodos();
      this.isEditModalActive = false;
    },
    DeleteAction(item) {
      this.$buefy.dialog.confirm({
        title: `Deleting Todo`,
        confirmText: "Delete Todo",
        type: "is-danger",
        hasIcon: true,
        message: `Are you sure you want to delete this item?`,
        onConfirm: () => {
          const index = this.todos.indexOf(item);
          this.todos.splice(index, 1);
          this.saveLocalStorageTodos();
        }
      });
    },
    deleteAllTodos() {
      this.$buefy.dialog.confirm({
        title: `Deleting Todos`,
        confirmText: "Delete Todos",
        type: "is-warning",
        hasIcon: true,
        message: `Are you sure you want to delete all the todos on your list?`,
        onConfirm: () => {
          this.todos = [];
          this.saveLocalStorageTodos();
        }
      });
    },
    findTodo(item) {
      return this.todos.find(todo => todo.id === item.id);
    },
    saveLocalStorageTodos() {
      localStorage.setItem("todos", JSON.stringify(this.todos));
      this.todos = JSON.parse(localStorage.getItem("todos"));
    }
  }
};
</script>