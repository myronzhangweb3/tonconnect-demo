<template>
  <div class="container">
    <div id="tonConnectButton"></div>
    <p v-if="connected" class="wallet-info">Wallet Address: {{ walletAddress }}</p>
    <div class="message-container">
      <label for="proofMessage">Proof Message:</label>
      <input type="text" id="proofMessage" ref="proofMessageRef" value="Hello Ton World">
    </div>
    <div class="proof-info" v-if="proof">
      <div style="text-align: center;">
        <hr class="light-divider"/>
        <h3>Proof Information:</h3>
      </div>
      <p><strong>Network:</strong> {{ proof.network }}</p>
      <p><strong>StateInit:</strong> ready</p>
      <p><strong>Timestamp:</strong> {{ proof.timestamp }}</p>
      <p><strong>Domain lengthBytes:</strong> {{ proof.domain.lengthBytes }}</p>
      <p><strong>Domain:</strong> {{ proof.domain.value }}</p>
      <p><strong>Signature:</strong> {{ proof.signature }}</p>
      <p><strong>Payload:</strong> {{ proof.payload }}</p>
      <p><strong>Verify:</strong>&nbsp;
        <a href="https://docs.ton.org/develop/dapps/ton-connect/sign#examples-of-ton-proof-verification"
           target="_blank">examples-of-ton-proof-verification</a>
      </p>
    </div>
    <div class="button-container">
      <button @click="handleSendTransaction" :disabled="!connected">
        Send Transaction
      </button>
      <button @click="handleBuildProof" :disabled="!connected">
        Build Proof
      </button>
    </div>
  </div>
</template>

<script>
import {ref, onMounted, onBeforeUnmount} from 'vue';
import {TonConnectUI} from '@tonconnect/ui';

export default {
  name: 'TonConnectButton',
  setup() {
    const tonConnectUI = ref(null);
    const walletAddress = ref('');
    const connected = ref(false);
    const proofMessageRef = ref(null);
    const proof = ref(null);

    onMounted(() => {
      // Initializing a TonConnectUI instance
      tonConnectUI.value = new TonConnectUI({
        manifestUrl: 'https://raw.githubusercontent.com/myronzhangweb3/tonconnect-demo/main/public/tonconnect-manifest.json',
        buttonRootId: 'tonConnectButton',
      });

      // Listening for connection status changes
      const unsubscribe = tonConnectUI.value.onStatusChange((wallet) => {
        if (wallet) {
          if (wallet.connectItems?.tonProof && 'proof' in wallet.connectItems.tonProof) {
            proof.value = wallet.connectItems.tonProof.proof;
            proof.value.network = wallet.account.chain;
            console.log("proof.value:", proof.value)
          }
          walletAddress.value = wallet.account.address;
          connected.value = true;
        } else {
          walletAddress.value = '';
          connected.value = false;
          proof.value = null;
        }
      });

      onBeforeUnmount(() => {
        unsubscribe();
      });
    });

    const handleSendTransaction = async () => {
      if (!connected.value) {
        alert('Please connect your wallet first');
        return;
      }

      const transaction = {
        validUntil: Math.floor(Date.now() / 1000) + 60, // 60 秒
        messages: [
          {
            address: "0QBZzQ3m8rceq9Y089I6BOz4i9oAscqSzbKOQd7haMWZfDZY",
            amount: "20000000",
          }
        ]
      };

      try {
        const result = await tonConnectUI.value.sendTransaction(transaction);
        console.log("Tx result: ", result);
        alert('Transaction sent successfully');
      } catch (e) {
        console.error(e);
        alert('Failed to send transaction');
      }
    };

    const handleBuildProof = async () => {
      console.log("handleBuildProof")
      if (!connected.value) {
        alert('Please connect your wallet first');
        return;
      }

      try {
        tonConnectUI.value.setConnectRequestParameters({
          state: 'ready',
          value: {
            tonProof: proofMessageRef.value.value
          }
        });
        await tonConnectUI.value.disconnect()
        await tonConnectUI.value.openModal()
      } catch (e) {
        console.error(e);
        alert('Signature failure');
      }
    };

    return {
      walletAddress,
      connected,
      handleSendTransaction,
      handleBuildProof,
      proofMessageRef,
      proof,
    };
  },
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}

#tonConnectButton {
  margin-bottom: 20px;
}

.message-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-bottom: 20px;
}

.message-container label {
  margin-bottom: 5px;
  font-weight: bold;
}

#proofMessage {
  width: 300px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.wallet-info {
  margin-bottom: 20px;
  font-weight: bold;
}

.proof-info {
  margin-bottom: 20px;
  text-align: left;
}

.proof-info h3 {
  margin-bottom: 10px;
}

.proof-info p {
  margin: 5px 0;
}

.button-container {
  display: flex;
  gap: 10px;
}

button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  cursor: pointer;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

button:not(:disabled):hover {
  background-color: #0056b3;
}

.light-divider {
  border: none;
  border-top: 1px solid #eee;
}
</style>
