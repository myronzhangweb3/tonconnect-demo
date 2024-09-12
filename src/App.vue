<template>
  <div id="app">
    <header>
      <h1>TonConnect Demo</h1>
    </header>
    <TonConnectButton />
  </div>
</template>

<script>
import TonConnectButton from './components/TonConnectDemo.vue';

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
  padding: 20px;
}
</style>
