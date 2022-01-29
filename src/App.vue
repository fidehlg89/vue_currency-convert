<template>
    <Dashboard />
</template>

<script>
import Dashboard from "./views/dashboard";
export default {
  name: "App",
  data: () => ({}),
  components: { Dashboard },
};
</script>

<style>
body,
html {
  background-color: #11118a !important;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
