<template>
  <div id="app">
    <h1>Список пользователей</h1>
    <MainTable />
  </div>
</template>

<script>
import { defineComponent } from 'vue';
import MainTable from './components/MainTable.vue';

export default defineComponent({
  name: 'App',
  components: {
    MainTable,
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
