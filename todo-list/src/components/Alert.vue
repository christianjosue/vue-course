<template>
    <div v-if="show" class="alert" :style="{backgroundColor}">
      <div>{{ message }}</div>
      <div @click="$emit('close')" class="close-alert">&times;</div>
    </div>
</template>

<script>
export default {
  props: {
    message: {
      required: true,
      type: String
    },
    show: {
      required: true,
      type: Boolean
    },
    type: {
      required: false,
      default: 'danger',
      validator(value) {
        return ['danger', 'warning', 'info', 'success'].includes(value)
      }
    }
  },
  computed: {
    backgroundColor() {
      const options = {
        danger: 'var(--danger-color)',
        warning: 'yellow',
        info: 'blue',
        success: 'green'
      }
      return options[this.type];
    }
  },
  emits: ['close']
};
</script>

<style scoped>
.alert {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding: 0 20px 0 20px;
  border-radius: 10px;
  height: 50px;
}

.close-alert {
  font-size: 50px;
  cursor: pointer;
}
</style>