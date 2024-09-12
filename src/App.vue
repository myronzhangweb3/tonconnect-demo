<template>
  <div id="app">
    <header>
      <h1>TonConnect Demo</h1>
    </header>
    <TonConnectButton />
  </div>
</template>

<script>
import TonConnectButton from './components/TonConnectButton.vue';

export default {
  name: 'App',
  components: {
    TonConnectButton,
  },
};
</script>

<style>
#app {
  text-align: center;
}

header {
  background-color: #42b983;
  padding: 20px;
  color: white;
}
</style>
