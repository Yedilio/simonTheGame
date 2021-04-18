<template>
  <div id="app">
    <Game />
  </div>
</template>

<script>
  import Game from "./components/Game";

  export default {
    name: 'App',
    components: {
      Game,
    }
  }
</script>

<style lang="scss" scoped>
  div {
    height: 100%;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
