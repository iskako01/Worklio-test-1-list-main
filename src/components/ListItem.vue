<template>
  <div v-for="(task, index) in requests" :key="index" class="card">
    <div class="list-item">
      <h3>{{ task.taskName }}</h3>
      <small>{{ task.date }}</small>
      <button class="btn danger" @click="deleteTask(task)">
        <IconTrash />
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import IconTrash from './svg/IconTrash.vue';
import Itasks from '../types/Itasks';

export default defineComponent({
  components: { IconTrash },
  props: {
    requests: {
      type: Array,
      required: true,
    },
  },
  emits: ['delete-task'],
  setup(_, { emit }) {
    const deleteTask = (task: Itasks) => {
      emit('delete-task', task);
    };

    return { deleteTask };
  },
});
</script>

<style lang="scss">
@use 'sass/global';
</style>
