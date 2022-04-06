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
      <button class="btn" @click="sortByValue">Sort by Value</button>
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
  name: 'List',
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
    const date = ref(format(new Date(), 'dd/MM/yyyy hh:mm a'));
    const filter = ref({ name: '' });

    const isActive = computed(() => taskName.value);
    // To filter the list
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
    //Function to sort the list alphabetically
    const sortByValue = () => {
      tasks.value.sort(function (a, b) {
        const taskNameA = a.taskName.toUpperCase();
        var taskNameB = b.taskName.toUpperCase();
        return taskNameA < taskNameB ? -1 : taskNameA > taskNameB ? 1 : 0;
      });
    };

    //Get data of localStorage
    const dataStorage: string | null = localStorage.getItem('tasks');
    if (dataStorage) {
      tasks.value = JSON.parse(dataStorage);
    }
    //Function for adding the task
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
    //Function for deleting a list item
    const deleteTask = (currentTask: Itasks) => {
      if (tasks.value.length) {
        console.log(currentTask);

        tasks.value = tasks.value.filter((task) => task.id !== currentTask.id);
        localStorage.setItem('tasks', JSON.stringify(tasks.value));
      }
    };

    watch(tasks.value, (n, o) => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value));
    });

    return {
      taskName,
      addTask,
      deleteTask,
      filter,
      requests,
      isActive,
      sortByValue,
    };
  },
});
</script>
<style lang="scss">
@use 'sass/global';
</style>
