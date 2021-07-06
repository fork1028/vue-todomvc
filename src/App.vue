<template>
  <div id="app">

    <TodoMVC msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import TodoMVC from './components/TodoMVC.vue'

export default {
  name: 'App',
  components: {
    TodoMVC
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
