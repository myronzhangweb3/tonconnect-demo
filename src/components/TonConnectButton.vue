<template>
  <div>
    <div id="tonConnectButton"></div>
    <p v-if="connected">钱包地址: {{ walletAddress }}</p>
    <button @click="handleSendTransaction" :disabled="!connected">
      发送交易
    </button>
  </div>
</template>

<script>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { TonConnectUI } from '@tonconnect/ui';

export default {
  name: 'TonConnectButton',
  setup() {
    const tonConnectUI = ref(null);
    const walletAddress = ref('');
    const connected = ref(false);

    onMounted(() => {
      // 初始化 TonConnectUI 实例
      tonConnectUI.value = new TonConnectUI({
        manifestUrl: 'https://<YOUR_APP_URL>/tonconnect-manifest.json',
        buttonRootId: 'tonConnectButton',
      });

      // 监听连接状态变化
      const unsubscribe = tonConnectUI.value.onStatusChange((wallet) => {
        if (wallet) {
          walletAddress.value = wallet.account.address;
          connected.value = true;
        } else {
          walletAddress.value = '';
          connected.value = false;
        }
      });

      onBeforeUnmount(() => {
        unsubscribe();
      });
    });

    const handleSendTransaction = async () => {
      if (!connected.value) {
        alert('请先连接钱包');
        return;
      }

      const transaction = {
        validUntil: Math.floor(Date.now() / 1000) + 60, // 60 秒
        messages: [
          {
            address: "EQBBJBB3HagsujBqVfqeDUPJ0kXjgTPLWPFFffuNXNiJL0aA",
            amount: "20000000",
          },
          {
            address: "EQDmnxDMhId6v1Ofg_h5KR5coWlFG6e86Ro3pc7Tq4CA0-Jn",
            amount: "60000000",
          }
        ]
      };

      try {
        const result = await tonConnectUI.value.sendTransaction(transaction);
        alert('交易发送成功');
      } catch (e) {
        console.error(e);
        alert('交易发送失败');
      }
    };

    return {
      walletAddress,
      connected,
      handleSendTransaction,
    };
  },
};
</script>

<style scoped>
button {
  margin-top: 10px;
}
</style>
