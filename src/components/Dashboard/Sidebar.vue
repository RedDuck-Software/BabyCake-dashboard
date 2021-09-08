<template>
  <div class="moonkat-sidebar">
    <div class="logo-and-buy">
      <div class="text-center">
        <router-link :to="{ name: 'Home' }">
          <img src="@/assets/images/beaglecakeLogo.png" class="logo" alt="moonKat" />
        </router-link>
      </div>
      <div class="mrat-text hide-on-mobile">beaglecake</div>
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
              <span>BUYSSMRAT</span>
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
        <a id="bscscan" :href="`https://bscscan.com/address/${signerAddress}`" target="_blank" style="margin-left: 10px"
          ><span style="margin-right: 3px"><i class="fa fa-clone"></i></span> View on BscScan Explorer
        </a>
      </div>
      <div class="text-2">Your MKAT balance:</div>
      <div class="text-3">
        MKAT
        <span> {{ myMkatBalance }} </span><br />
        (
        <span>
          {{ myMkatBalanceInBUSD }}
        </span>
        $)
      </div>
    </div>
    <div class="address-info sidebar-menu">
      <div v-for="(item, id) in menuItems" v-bind:key="id" class="sidebar-menu-item">
        <a href="">
          <img :src="`@/assets/images/${item.logoPath}.png`" alt="" />
          <span>{{ item.name }}</span>
        </a>
        <div v-if="id < menuItems.length - 1"></div>
      </div>
    </div>
    <div class="button-logout-wrapper hide-on-mobile" style="cursor: pointer" @click="logout()">
      <a target="_blank" class="button-custom-new button-sidebar"
        ><i class="fa fa-sign-out"></i>
        <div class="buyCard">
          <div class="buyCardBody">
            <img src="@/assets/images/logout.png" alt="" />
            <span>LOGOUT</span>
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
      menuItems: [
        {
          name: "Homepage",
          logoPath: "home",
          href: "",
        },
        {
          name: "Guide",
          logoPath: "book",
          href: "",
        },
        {
          name: "Disclaimer",
          logoPath: "vosklznak",
          href: "",
        },
        {
          name: "Contract",
          logoPath: "charts",
          href: "",
        },
        {
          name: "Chart",
          logoPath: "chartsup",
          href: "",
        },
      ],
    };
  },
  computed: {
    ...mapGetters(["signerAddress"]),
    ...mapGetters(["walletProviderType"]),
  },
  watch: {
    contract() {
      this.loadContractInfo();
    },
  },
  mounted() {
    this.canCopy = !!navigator.clipboard;
    this.loadContractInfo();

    setTimeout(async function() {
      await this.loadContractInfo();
    }, 600000);
  },
  methods: {
    async loadContractInfo() {
      console.log(this.walletProviderType);

      const service = new MetamaskService(await MetamaskService.createWalletProviderFromType(this.walletProviderType));
      await service.updateMKATBusdValue();

      this.service = service;
      this.mkatContract = await service.getContractInstance(CONTRACT_ADDRESS);
      this.myMkatBalance = ethers.utils.formatUnits(await this.mkatContract.balanceOf(this.signerAddress), 9);
      this.myMkatBalanceInBUSD = await service.getMkatValueInBUSD(ethers.utils.parseUnits(this.myMkatBalance, 9));
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

.sidebar-menu {
  display: flex;
  flex-direction: column;
  align-items: start;
}

.sidebar-menu-item {
  display: flex;
  align-items: center;
  gap: 5px;
}
</style>
