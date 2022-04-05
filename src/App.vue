<template>
  <List />
</template>

<script lang="ts">
import { defineComponent } from 'vue';

import List from './components/List.vue';

export default defineComponent({
  name: 'App',

  components: { List },

  setup() {
    // Setup
  },
});
</script>

<style lang="scss" module>
@use 'sass/global';
</style>

