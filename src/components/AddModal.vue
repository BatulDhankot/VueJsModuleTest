<template>
  <transition name="modal-animation">
    <div v-show="modalActive" class="modal">
      <transition name="modal-animation-inner">
        <div v-show="modalActive" class="modal-inner">
          <!-- Modal Content -->
          <slot /> 
          
        </div>
      </transition>
    </div>
  </transition>
</template>

<script setup lang="ts">
defineProps({
  modalActive: {
    type: Boolean,
    required: false,
  },
})
const emit = defineEmits(['close'])
</script>