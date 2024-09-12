<template>
  <div id="app">
    <header>
      <h1>TonConnect Demo</h1>
    </header>
    <TonConnectButton />
  </div>

  <div style="text-align: left;">
    <span>Workflows：</span>
    <div><span>1. Front-end generation of Proof</span></div>
    <div><span>2. Back-end validation of Proof</span></div>

    <div>
      Source code: <a href="https://github.com/myronzhangweb3/tonconnect-demo" target="_blank">This Website</a>, <a href="https://github.com/ton-connect/demo-dapp-backend/blob/e353d07ff1b3be3b64f6f8498addc76509821d67/proof.go#L82" target="_blank">Backend(Golang)</a>
    </div>
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
