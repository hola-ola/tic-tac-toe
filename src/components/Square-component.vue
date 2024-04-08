<script setup lang="ts">
import { computed } from 'vue'

const props = defineProps({
  rowIndex: {
    type: Number,
    required: true
  },
  cellIndex: {
    type: Number,
    required: true
  },
  sign: {
    type: String,
    default: null
  },
  disabled: {
    type: Boolean,
    default: false
  }
})

const cursor = computed(() => (props.disabled ? 'disabled' : 'pointer'))
</script>

<template>
  <div class="square" :class="['square__${cursor}']">
    <p>{{ sign }}</p>
  </div>
</template>

<style scoped>
.square {
  @apply w-full h-full flex flex-row justify-center items-center;

  p {
    @apply text-8xl;
    color: #170a1c;
  }

  &__pointer {
    @apply cursor-pointer;
  }

  &__disabled {
    @apply cursor-not-allowed;
  }
}
</style>