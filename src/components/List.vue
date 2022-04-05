<template>
  <div class="container">
    <div class="list">
      <h1>Worklio List</h1>
      <form class="form" @submit.prevent="addTask">
        <div class="form-control">
          <label for="name">Enter the task name</label>
          <input id="name" v-model.trim="taskName" type="text" />
        </div>
        <div>
          <button v-if="isActive" class="btn primary" @keydown.enter="addTask">
            <IconAdd />
          </button>
        </div>
      </form>

      <hr />
      <SearchBar v-model="filter" />
      <ListItem :requests="requests" @delete-task="deleteTask" />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch, computed } from 'vue';
import { format } from 'date-fns';
import Itasks from '../types/Itasks';
import ListItem from './ListItem.vue';
import SearchBar from './SearchBar.vue';
import IconAdd from './svg/IconAdd.vue';

export default defineComponent({
  components: { ListItem, IconAdd, SearchBar },
  setup() {
    const taskName = ref('');
    const tasks = ref<Itasks[]>([
      {
        taskName: '',
        date: '',
        id: Date.now(),
      },
    ]);
    const date = ref(format(new Date(), 'MM/dd/yyyy hh:mm a'));
    const filter = ref({ name: '' });

    const isActive = computed(() => taskName.value);

    const requests = computed(() =>
      tasks.value.filter((request) => {
        if (filter.value.name) {
          return request.taskName
            .toUpperCase()
            .includes(filter.value.name.toUpperCase());
        }
        return request;
      })
    );

    //Get data of localStorage
    const dataStorage: string | null = localStorage.getItem('tasks');
    if (dataStorage) {
      tasks.value = JSON.parse(dataStorage);
    }
    //Add task
    const addTask = () => {
      if (taskName.value != '') {
        const tasksData = {
          taskName: taskName.value,
          date: date.value,
          id: Date.now(),
        };
        tasks.value.push(tasksData);
        taskName.value = '';
      }
    };
    //Delete task
    const deleteTask = (currentTask: Itasks) => {
      if (tasks.value.length) {
        console.log(currentTask);

        tasks.value = tasks.value.filter((task) => task.id !== currentTask.id);
        localStorage.setItem('tasks', JSON.stringify(tasks.value));
      }
    };
    // eslint-disable-next-line no-undef
    watch(tasks.value, (n, o) => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value));
    });

    return { taskName, tasks, addTask, deleteTask, filter, requests, isActive };
  },
});
</script>
<style lang="scss">
@use 'sass/global';
</style>
