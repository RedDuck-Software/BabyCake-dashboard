<template>
  <div class="moonkat-sidebar">
    <div class="logo-and-buy">
      <div class="text-center">
        <router-link :to="{ name: 'Home' }">
          <img src="@/assets/images/beaglecakeLogo.png" class="logo" alt="moonKat" />
        </router-link>
      </div>
      <div class="mrat-text hide-on-mobile" style="color: #190053">beaglecake</div>
      <div class="mrat-desc">A new way to earn BNB</div>
      <div class="button-buy-mrat hide-on-mobile">
        <a
          href="https://exchange.pancakeswap.finance/#/swap?outputCurrency=0x38bd8cd90374dbc903aed9d2ee28f5ab856342ce"
          target="_blank"
          class="button-custom-new button-sidebar"
          ><i class="fa fa-shopping-cart"></i>
          <div class="buyCard">
            <div class="buyCardBody">
              <img src="@/assets/images/shop.png" alt="" />
              <span class="buysSmart">BUYSSMRAT</span>
            </div>
          </div>
        </a>
      </div>
    </div>
    <div class="address-info">
      <div class="text-1 hide-on-mobile">Address information</div>
      <div class="text-2 hide-on-mobile">Your address</div>
      <div id="myAddress" class="text-3 hide-on-mobile">
        <div class="d-flex">
          <span id="my-address" ref="myAddr" class="truncate">{{ signerAddress }}</span>
          <input id="testing-code" type="hidden" :value="signerAddress" />
        </div>
      </div>
      <div class="text-4 hide-on-mobile">
        <span id="copy-address" @click="copyAddress()">
          <span style="margin-right: 3px"> <i class="fa fa-clone"></i></span> Copy address
        </span>
        <a
          id="bscscan"
          :href="`https://etherscan.io/address/${signerAddress}`"
          target="_blank"
          style="margin-left: 10px;"
          ><span style="margin-right: 3px"><i class="fa fa-clone"></i></span> View on Etherscan
        </a>
      </div>
      <div class="text-2">Your MKAT balance:</div>
      <div class="text-3">
        BBC
        <span> {{ myMkatBalance }} </span><br />
        (
        <span>
          {{ myMkatBalanceInBUSD }}
        </span>
        $)
      </div>
    </div>
    <div class="address-info sidebar-menu">
      <div class="sidebar-menu-item sidebar-menu-item-nonlast">
        <a href="">
          <img src="@/assets/images/home.png" alt="" />
          <span>Homepage</span>
        </a>
      </div>
      <div class="sidebar-menu-item sidebar-menu-item-nonlast">
        <a href="">
          <img src="@/assets/images/book.png" alt="" />
          <span>Guide</span>
        </a>
      </div>
      <div class="sidebar-menu-item sidebar-menu-item-nonlast">
        <a href="">
          <img src="@/assets/images/vosklznak.png" alt="" />
          <span>Disclaimer</span>
        </a>
      </div>
      <div class="sidebar-menu-item sidebar-menu-item-nonlast">
        <a href="">
          <img src="@/assets/images/charts.png" alt="" />
          <span>Contract</span>
        </a>
      </div>
      <div class="sidebar-menu-item">
        <a href="">
          <img src="@/assets/images/chartsup.png" alt="" />
          <span>Chart</span>
        </a>
      </div>
    </div>
    <div class="sidebar-locale">
      <img src="@/assets/images/globus.png" alt="" />
      <span>English</span>
    </div>
    <div class="button-logout-wrapper hide-on-mobile" style="cursor: pointer" @click="logout()">
      <a target="_blank" class="button-custom-new button-sidebar"
        ><i class="fa fa-sign-out"></i>
        <div class="buyCard">
          <div class="buyCardBody">
            <img src="@/assets/images/logout.png" alt="" />
            <span class="logoutText">LOGOUT</span>
          </div>
        </div>
      </a>
    </div>
  </div>
</template>

<script>
import { CONTRACT_ADDRESS } from "@/constants";
import { ContractFactory, ethers } from "ethers";
import { mapGetters } from "vuex";
import MetamaskService from "@/MetamaskService";
import { WalletType } from "../../MetamaskService";

export default {
  name: "Sidebar",
  props: {
    contract: {
      type: ContractFactory,
      required: true,
    },
  },
  data() {
    return {
      canCopy: false,
      myMkatBalance: "...",
      myMkatBalanceInBUSD: "0.00",
      mkatContract: null,
      service: null,
    };
  },
  computed: {
    ...mapGetters(["signerAddress"]),
    ...mapGetters(["walletProviderType"]),
  },
  watch: {
    contract() {
      this.updateUserBalances();
    },
  },
  async mounted() {
    this.canCopy = !!navigator.clipboard;

    this.service = new MetamaskService(await MetamaskService.createWalletProviderFromType(this.walletProviderType));
    await this.service.initialize();

    this.mkatContract = this.service.getTokenContractInstance();

    this.updateUserBalances();

    setTimeout(this.updateUserBalance, 600000);
  },
  methods: {
    async updateUserBalances() {
      this.myMkatBalance = ethers.utils.formatUnits(await this.mkatContract.balanceOf(this.signerAddress), 18);
      this.myMkatBalanceInBUSD = ethers.utils.formatUnits(
        await this.service.getMkatValueInBUSD(ethers.utils.parseUnits(this.myMkatBalance, 18)),
        18
      );
    },
    async copyAddress() {
      const address = this.$refs.myAddr;
      await navigator.clipboard.writeText(address.innerHTML);
      alert("Copied!");
    },
    async logout() {
      if (this.walletProviderType == WalletType.WalletConnect) this.service.walletProvider.disconnect();

      this.$store.commit("logout");
      this.$router.replace("/connect-wallet");
    },
  },
};
</script>

<style scoped>
.buyCardBody {
  display: flex;
  align-items: center;
  gap: 5px;
}

.buyCard {
  display: inline-block;
}

.buysSmart {
  font-weight: 500;
  color: #c4c6e7;
}

.sidebar-menu {
  display: flex;
  flex-direction: column;
  align-items: start;
}
.sidebar-menu-item {
  width: 100%;
}
.sidebar-menu-item a {
  display: flex;
  color: #ffffff;
  align-items: center;
  gap: 5px;
  padding: 5px 0;
  padding-left: 25px;
}
.sidebar-menu-item img {
  height: 20px;
}
.sidebar-menu-item-nonlast {
  border-bottom: 1px solid #ffffff;
}
.sidebar-locale {
  display: flex;
  align-items: center;
  background-color: #190053;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  margin-top: 25px;
  color: #fff;
  border-radius: 20px;
  padding: 5px 15px;
  padding-left: 35px;
  gap: 5px;
}
.sidebar-locale img {
  height: 20px;
}

.logoutText {
  color: #c4c6e7;
  font-weight: 500;
}

a#bscscan {
  color: #c4c6e7;
}
a#bscscan:hover {
  color: #fff;
}
</style>
