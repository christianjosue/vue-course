<template>
    <Modal :show="show" @close="$emit('close')">
      <template #header>
        <h2>Edit Todo</h2>
      </template>

      <template #content>
        <form class="editTodoForm">
          <div>
            <label>Todo title</label>
            <input type="text" :value="modelValue" @input="$emit('update:modelValue', $event.target.value)">
          </div>
        </form>
      </template>

      <template #footer>
        <div class="edit-todo-modal-footer">
          <Btn class="edit-todo-submit-btn" @click="$emit('submit')">Submit</Btn>
          <Btn type="danger" @click="$emit('close')">Close</Btn>
        </div>
      </template>
    </Modal>
</template>

<script>
import Modal from './Modal.vue';
import Btn from './Btn.vue';

export default {
    components: {
        Modal, 
        Btn
    },
    props: ['modelValue', 'show'],
    emits: ['close', 'submit', 'update:modelValue']
}
</script>

<style scoped>
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