<template>
  <div id="app">
    <csv-to-table />
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import CsvToTable from "./components/CsvToTable.vue";

export default {
  name: "app",
  components: {
    CsvToTable
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
