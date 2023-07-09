<template>
  <Navbar />
  <main>
    <Modal :show="editTodoForm.show" @close="editTodoForm.show = false">
      <template #header>
        <h2>Edit Todo</h2>
      </template>

      <template #content>
        <form class="editTodoForm">
          <div>
            <label>Todo title</label>
            <input type="text" v-model="editTodoForm.todo.title">
          </div>
        </form>
      </template>

      <template #footer>
        <div class="edit-todo-modal-footer">
          <Btn class="edit-todo-submit-btn" @click="updateTodo">Submit</Btn>
          <Btn type="danger" @click="editTodoForm.show = false">Close</Btn>
        </div>
      </template>
    </Modal>
    <Alert
      :show="alert.show"
      :message="alert.message"
      :type="alert.type"
      @close="alert.show = false"
      type="danger"
    />
    <section>
      <AddTodoForm @submit="addTodo" />
    </section>

    <section>
      <Todo 
        v-for="todo in todos"
        :key="todo.id"
        :title="todo.title"
        @remove="removeTodo(todo.id)"
        @edit="showEditTodoForm(todo)"
      />
    </section>
  </main>
</template>

<script>
import Alert from "./components/Alert.vue";
import Navbar from "./components/Navbar.vue";
import AddTodoForm from "./components/AddTodoForm.vue";
import Todo from "./components/Todo.vue";
import Modal from "./components/Modal.vue";
import Btn from "./components/Btn.vue";
import axios from 'axios';

export default {
  components: {
    Alert,
    Navbar,
    AddTodoForm,
    Todo,
    Modal,
    Btn
},

  data() {
    return {
      todos: [],
      alert: {
        show: false,
        message: "",
        type: "danger"
      },
      editTodoForm: {
        show: false,
        todo: {
          id: 0,
          title: ""
        }
      }
    };
  },

  created() {
    this.fetchTodo();
  },

  methods: {
    async fetchTodo() {
      try {
        const res = await axios.get('http://localhost:8080/todos');
        this.todos = res.data;
      } catch (err) {
        this.showAlert(err.message);
      }
    },

    showAlert(message, type = "danger") {
      this.alert.show = true;
      this.alert.message = message;
      this.alert.type = type;
    },

    async addTodo(title) {
      if (title === "") {
        this.showAlert("Todo title is required");
        return;
      }

      const res = await axios.post('http://localhost:8080/todos', {
        title,
      });

      this.fetchTodo();
    },

    async removeTodo(id) {
      await axios.delete(`http://localhost:8080/todos/${id}`);
      this.fetchTodo();
    },

    showEditTodoForm(todo) {
      this.editTodoForm.show = true;
      this.editTodoForm.todo = {...todo};
    },

    updateTodo() {
      if (this.editTodoForm.todo.title === "") {
        this.alert.show = true;
        this.editTodoForm.show = false;
        return;
      }
      const todo = this.todos.find(todo => todo.id === this.editTodoForm.todo.id);
      todo.title = this.editTodoForm.todo.title;
      this.editTodoForm.show = false;
      if (this.alert.show) {
        this.alert.show = false;
      }
    }
  },
};
</script>

<style scoped>
main {
  box-sizing: border-box;
  padding: 50px 100px;
}

.editTodoForm > input {
  width: 100%;
  height: 30px;
  border: 1px solid var(--accent-color);
}

.edit-todo-modal-footer {
  display: flex;
  justify-content: flex-end;
  padding: 10px;
}

.edit-todo-submit-btn {
  margin-right: 5px;
}
</style>
