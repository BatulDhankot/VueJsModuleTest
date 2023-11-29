<template>
  <transition name="modal-animation">
    <div v-show="isDeleteDialogVisible" class="modal">
      <transition name="modal-animation-inner">
        <div v-show="isDeleteDialogVisible" class="modal-inner">
          <!-- Modal Content -->
          <slot />
          
        </div>
      </transition>
    </div>
  </transition>
</template>

<script setup lang="ts">
defineProps({
  isDeleteDialogVisible: {
    type: Boolean,
    required: false,
  },
})
const emit = defineEmits(['close'])
</script>