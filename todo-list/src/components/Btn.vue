<template>
  <button 
    @click.prevent="$emit('click')" 
    :style="{ backgroundColor }"
    :class="{circle: applyCircleClass}"
  >
    <slot />
  </button>
</template>

<script>
export default {
  props: {
    type: {
      required: false,
      default: 'success',
      validator(value) {
        return ['danger', 'warning', 'info', 'success', 'secondary'].includes(value)
      }
    },
    circle: {
      default: false,
      type: Boolean
    }
  },
  computed: {
    backgroundColor() {
      const options = {
        danger: 'var(--danger-color)',
        warning: 'var(--warning-color)',
        info: 'var(--info-color)',
        success: 'var(--accent-color)',
        secondary: 'var(--secondary-color)'
      }
      return options[this.type];
    },
    applyCircleClass() {
      return this.circle;
    }
  },
  emits: ['click']
}
</script>

<style scoped>
button {
  color: var(--text-color);
  border: none;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
}

.circle {
  border-radius: 50%;
}

button:disabled {
  opacity: 70%;
}
</style>