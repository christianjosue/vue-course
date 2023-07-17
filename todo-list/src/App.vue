<template>
  <Navbar />
  <main>
    <EditTodoForm 
      :show="editTodoForm.show"
      @close="editTodoForm.show == false"
      @submit="updateTodo"
      v-model="editTodoForm.todo.title"
    />
    <Alert
      :show="alert.show"
      :message="alert.message"
      :type="alert.type"
      @close="alert.show = false"
      type="danger"
    />
    <section>
      <AddTodoForm :isLoading="isPostingTodo" @submit="addTodo" />
    </section>

    <section>
      <Spinner class="spiner" v-if="isLoading"/>
      <div v-else>
        <Todo 
          v-for="todo in todos"
          :key="todo.id"
          :title="todo.title"
          @remove="removeTodo(todo.id)"
          @edit="showEditTodoForm(todo)"
        />
      </div>
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
import Spinner from './components/Spinner.vue';
import EditTodoForm from "./components/EditTodoForm.vue";
import axios from 'axios';

export default {
  components: {
    Alert,
    Navbar,
    AddTodoForm,
    Todo,
    Modal,
    Btn,
    Spinner,
    EditTodoForm
},

  data() {
    return {
      todos: [],
      alert: {
        show: false,
        message: "",
        type: "danger"
      },
      isLoading: false,
      isPostingTodo: false,
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
      this.isLoading = true;
      try {
        const res = await axios.get('/api/todos');
        this.todos = res.data;
      } catch (err) {
        this.showAlert(err.message);
      }
      this.isLoading = false;
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

      this.isPostingTodo = true;
      const res = await axios.post('/api/todos', {
        title,
      });
      this.isPostingTodo = false;

      this.fetchTodo();
    },

    async removeTodo(id) {
      await axios.delete(`/api/todos/${id}`);
      this.fetchTodo();
    },

    showEditTodoForm(todo) {
      this.editTodoForm.show = true;
      this.editTodoForm.todo = {...todo};
    },

    async updateTodo() {
      try {
        const {id, title} = this.editTodoForm.todo;
        await axios.put(`/api/todos/${id}`, {title});

        const todo = this.todos.find(todo => todo.id === this.editTodoForm.todo.id);
        todo.title = this.editTodoForm.todo.title;
      } catch (e) {
        this.showAlert("Failed updating todo");
      }
      this.editTodoForm.show = false;
    }
  },
};
</script>

<style scoped>
main {
  box-sizing: border-box;
  padding: 50px 100px;
}

.spinner {
  margin: auto;
  margin-top: 30px;
}
</style>
