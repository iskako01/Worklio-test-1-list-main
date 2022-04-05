<template>
  <div class="filter">
    <div class="form-control">
      <input v-model="name" type="text" placeholder="Search" />
    </div>
    <button v-if="isActive" class="btn warning" @click="reset">
      <IconCancel />
    </button>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch, computed } from 'vue';
import IconCancel from './svg/IconCancel.vue';

export default defineComponent({
  components: { IconCancel },
  props: ['modelValue'],
  emits: ['update:modelValue'],
  setup(_, { emit }) {
    const name = ref('');
    const isActive = computed(() => name.value);

    document.onkeydown = (e) => {
      if (e.key === 'Escape') {
        reset();
      }
    };

    const reset = () => {
      name.value = '';
    };

    watch(name, (value) => {
      emit('update:modelValue', {
        name: value,
      });
    });
    return { name, reset, isActive };
  },
});
</script>

<style></style>
