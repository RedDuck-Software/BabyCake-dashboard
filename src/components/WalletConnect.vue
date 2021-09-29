<template>
  <section class="connect-wallet">
    <div class="logo">
      <img src="@/assets/images/beaglecakeLogo.png" class="logo-section" />
      <h1 class="logo-title">beaglecake</h1>
    </div>
    <div class="container">
      <div class="row">
        <div class="section-2">
          <div class="connect-to-wallet">
            <img src="@/assets/images/beaglecakeLogo.png" class="logo-section" />
            <div class="text-show-1">Welcome to Beagle Cake Dapp</div>
            <div class="content-main">
              <div class="text-1">You are not connected to Beagle Cake Dapp yet</div>
              <div class="line-2">
                To use the Dapp, make sure: <br /><span
                  >You are using the <span class="text-color">Binance Chain</span> network</span
                ><br /><span>and you need to connect wallet to use</span><br />
                <div style="margin-top: 40px;">
                  <a href="https://docs.binance.org/smart-chain/wallet/metamask.html" target="_blank"> Metamask</a
                  ><br />
                  <a href="https://docs.binance.org/wallets/trust-wallet.html" target="_blank">Trust Wallet</a><br />
                  <a href="https://docs.binance.org/smart-chain/wallet/binance.html" target="_blank"
                    >Binance Chain Extention Wallet</a
                  ><br />
                  <a href="https://docs.binance.org/wallets/safepal.html" target="_blank">SafePal</a><br />
                </div>
              </div>
            </div>
            <div class="button-wrapper">
              <button
                id="connectBtn"
                type="button"
                class="el-button button-custom-new el-button--primary el-button--medium"
                @click="connectMetamask()"
              >
                <i class="el-icon-connection"></i>
                <span>Connect to a wallet </span>
              </button>
              <br />

              <!---->

              <!--              <div class="how-to-connect">-->
              <!-- <a  href="https://guide.MoonKat.finance/how-to-connect-MoonKats-dapp-to-wallet" target="_blank"> How to connect MoonKat's Dapp to wallet </a> -->
              <!--              </div>-->
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { ethers } from "ethers";
import { mapGetters } from "vuex";
import WalletConnectProvider from "@walletconnect/web3-provider";
import { WalletType } from "../MetamaskService";

export default {
  name: "WalletConnect",

  data() {
    return {
      isMobile: false,
    };
  },
  computed: {
    ...mapGetters(["signerAddress"]),
    ...mapGetters(["walletProviderType"]),
  },
  mounted() {
    this.detectMobile();

    console.log("Signer address", this.signerAddress);

    if (this.signerAddress && this.walletProviderType) {
      this.$router.replace({ path: "dashboard" });
    }
  },
  methods: {
    async connectMetamask() {
      if (window.ethereum != undefined) {
        const accounts = await window.ethereum.request({ method: "eth_requestAccounts" });
        this.$store.commit("updateSignerAddress", accounts[0]);
        this.$store.commit("updateWalletProviderType", WalletType.Metamask);

        this.$router.push({ path: "dashboard" });
      } else {
        if (!this.isAndroid && !this.isIos) alert("Please install MetaMask!");
      }
    },
    async connectWalletConnect() {
      const walletConnectProvider = new WalletConnectProvider({
        rpc: { 56: "https://bsc-dataseed.binance.org/" },
        chainId: 56,
        qrcode: true, // Required
      });

      await walletConnectProvider.enable();

      await this.updateDataOnAccountChange(walletConnectProvider);

      this.$router.push({ path: "dashboard" });

      // Subscribe to accounts change
      walletConnectProvider.on("accountsChanged", accounts => {
        this.updateDataOnAccountChange(walletConnectProvider);
      });

      // Subscribe to chainId change
      walletConnectProvider.on("chainChanged", chainId => {
        console.log("chain changed: ", chainId);
      });

      // Subscribe to session disconnection
      walletConnectProvider.on("disconnect", (code, reason) => {
        console.log("Disconnect", reason);

        this.$store.commit("logout");
        this.$router.push({ path: "connect-wallet" });
      });
    },
    async updateDataOnAccountChange(walletConnectProvider) {
      const provider = new ethers.providers.Web3Provider(walletConnectProvider);

      console.log("web3 provider:", provider);
      console.log("wallet provider:", walletConnectProvider);

      const signer = provider.getSigner();
      console.log("signer:", signer);
      const address = await signer.getAddress();

      console.log("signer address:", address);

      this.$store.commit("updateSignerAddress", address);
      this.$store.commit("updateWalletProviderType", WalletType.WalletConnect);
    },
    detectMobile() {
      if (/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
        this.isMobile = true;
      }
    },
  },
};
</script>

<style scoped>
html {
  height: 100%;
}

.logo {
  display: flex;
  margin-top: -100px;
  margin-left: 30px;

  margin-bottom: 100px;
}

.logo .logo-section {
  width: 60px;
  height: 60px;
}

.logo .logo-title {
  margin: 15px;
  font-size: 26px;
  text-transform: uppercase;
  font-weight: 700;
}

.section-2 .button-wrapper {
  margin-top: 20px;
  display: flex;
  flex-wrap: wrap;
}

.button-custom-new {
  border-radius: 10px !important;
  font-size: 15px !important;
  -webkit-transition: all 0.3s linear;
  transition: all 0.3s linear;
  flex: 1 0 100%;
  height: 44px;
}

.line-2 span .text-color {
  color: #00aaa6;
}

.button-wrapper {
  margin-bottom: 40px;
}

#connectBtn {
  border-radius: 20px !important;
  display: inline-block;
  line-height: 34px;
  white-space: nowrap;
  cursor: pointer;
  border: none;
  background-image: linear-gradient(-90deg, rgb(58, 179, 176) 0%, rgb(110, 99, 157) 100%);
  color: #c4c6e7;
  -webkit-appearance: none;
  text-align: center;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  outline: none;
  margin: 0;
  -webkit-transition: 0.1s;
  transition: 0.1s;
  font-weight: 400;
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
  padding: 0;

  font-size: 22px !important;
  font-weight: 500;
  font-family: "Rancho", cursive;
}

#connectBtn:active,
#connectBtn:focus,
#connectBtn:hover {
  background-size: 200% auto;
  background-position: 100%;
  background-image: linear-gradient(90deg, rgb(58, 179, 176) 0%, rgb(110, 99, 157) 100%);
  color: #ffffffd3;
  transition: all 0.1s linear;
}
</style>
