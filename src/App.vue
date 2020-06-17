<template>
  <div id="app">
    <Container />
  </div>
</template>

<script>
import Container from "./components/Container";

export default {
  name: "App",
  components: {
    Container
  }
};
</script>
<style>
* {
  margin: 0;
}
body {
  background-image: url("./assets/main-pattern.png");
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}
</style>
