<template>
  <section class="dashboard-content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-sm-3">
          <Sidebar :contract="contract" />
        </div>
        <div class="col-sm-9">
          <div class="row">
            <div class="col-sm-12">
              <div class="row">
                <div class="col-sm-6">
                  <div class="mt-3">
                    <div class="tab-card-header">
                      <ul id="myTab" class="nav nav-tabs card-header-tabs" role="tablist">
                        <li class="nav-item">
                          <a
                            id="one-tab"
                            class="nav-link"
                            :class="{ 'active show': isActive('one') }"
                            data-toggle="tab"
                            href="#"
                            role="tab"
                            aria-controls="One"
                            aria-selected="true"
                            @click.prevent="setActive('one')"
                            >Claim BNB</a
                          >
                        </li>
                        <li class="nav-item">
                          <a
                            id="two-tab"
                            class="nav-link"
                            :class="{ 'active show': isActive('two') }"
                            data-toggle="tab"
                            href="#two"
                            role="tab"
                            aria-controls="Two"
                            aria-selected="false"
                            @click.prevent="setActive('two')"
                            >Statistic</a
                          >
                        </li>
                      </ul>
                    </div>
                  </div>
                </div>
                <div class="col-sm-6 social-links-container">
                  <div class="social-links">
                    <a href=""><img src="@/assets/images/Youtube_logo.png" alt=""/></a>
                    <a href=""><img src="@/assets/images/Tiktok_logo.png" alt=""/></a>
                    <a href=""><img src="@/assets/images/Reddit_logo.png" alt=""/></a>
                    <a href=""><img src="@/assets/images/Facebook_logo.png" alt=""/></a>
                    <a href=""><img src="@/assets/images/Discord_logo.png" alt=""/></a>
                    <a href=""><img src="@/assets/images/Twitter_logo.png" alt=""/></a>
                    <a href=""><img src="@/assets/images/Instagram_logo.png" alt=""/></a>
                    <a href=""><img src="@/assets/images/Telegram_logo.png" alt=""/></a>
                  </div>
                </div>
              </div>
              <div class="mt-3">
                <div id="myTabContent" class="tab-content info-container">
                  <div id="one" class="tab-pane fade p-3 claim-reward" :class="{ 'active show': isActive('one') }">
                    <div class="claim-reward-content">
                      <div v-if="!isRewardClaimAvailable" class="disabled-overlay w-100 h-100"></div>
                      <div class="row">
                        <div class="col-sm-3 p-1">
                          <div class="title-1" style="color: #190053">Reward Pool</div>
                          <div class="image-content">
                            <img src="@/assets/images/beaglepancakesx.png" alt="image-pancake" />
                            <img src="@/assets/images/hand.png" class="image-reward-pool" />
                          </div>
                          <div class="title-2" style="color: #190053">CAKE <span class="card-panel-num"> </span></div>
                        </div>
                        <div class="col-sm-9 p-2">
                          <div class="title-1">
                            Available reward: <span class="bold">{{ cakeAvailableReward }} CAKE</span>
                          </div>

                          <div class="title-1 mt-3">
                            Total rewards: <span class="bold">{{ cakeTotalGainedReward }} CAKE</span>
                          </div>


                          <div v-if="nextClaimDate != '0'" class="title-2">
                            Next claim date: <span class="bold">{{ nextClaimDate }} </span>
                          </div>
                          <div class="title-noted">
                            *pool is always changing based on buys, sells, and collects by others, learn more here
                            <span
                              ><a href="#" target="_blank"><i class="fa fa-question-circle"></i></a
                            ></span>
                          </div>
                          <div class="button-wrapper hide-on-mobile">
                            <div>
                              <button
                                id="claim-button"
                                type="button"
                                :disabled="cakeAvailableReward === '0'"
                                class="el-button button-custom-new el-button--default el-button--medium is-disabled"
                                @click="claimMyReward()"
                              >
                                <div>
                                  <i class="fa fa-gift"></i>
                                  <div class="buyCard">
                                    <div class="buyCardBody">
                                      <span>Claim my Reward</span>
                                    </div>
                                  </div>
                                </div>
                              </button>
                            </div>
                            <span v-if="balanceOfAddress == 0" class="text-warning mt-4"
                              >*Claim is not available. You must to own BabyCake token to start gaining your static
                              rewards</span
                            >
                          </div>
                        </div>
                      </div>
                    </div>
                    <div class="statistic-wrapper">
                      <div class="item-statistic">
                        <div class="row">
                          <div class="col-sm-4 p-1">
                            <img src="@/assets/images/beaglepancake2.png" class="img-icon" />
                          </div>
                          <div class="col-sm-8 p-2">
                            <div class="text-1" style="color: #190053">Total Liquidity Pool</div>
                            <div class="text-2" style="color: #190053">
                              <span>$</span><span class="card-panel-num"> {{ totalLiquidityPoolUSD }} </span>
                            </div>
                          </div>
                        </div>
                      </div>
                      <div class="item-statistic">
                        <div class="row">
                          <div class="col-sm-4 p-1">
                            <img src="@/assets/images/beaglepool.png" class="img-icon" />
                          </div>
                          <div class="col-sm-8 p-2">
                            <div class="text-1" style="color: #190053">Total BNB in liquidity pool</div>
                            <div class="text-2" style="color: #190053">
                              <span> {{ totalBnbInPool }} </span><span class="card-panel-num"> BNB </span>
                            </div>
                          </div>
                        </div>
                      </div>
                      <div class="item-statistic">
                        <div class="row">
                          <div class="col-sm-4 p-1">
                            <img src="@/assets/images/beaglecakeLogo.png" class="img-icon" />
                          </div>
                          <div class="col-sm-8 p-2">
                            <div class="text-1" style="color: #190053">Current 100,000 CAKE price</div>
                            <div class="text-2" style="color: #190053">
                              <span></span><span class="card-panel-num">$ {{ hundredThousandMKATUSD }} </span>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                    <div
                      class="sweet-modal-content sweet-modal-overlay theme-light sweet-modal-clickable"
                      style="display: none"
                    >
                      <div class="sweet-modal theme-light has-content is-alert">
                        <div class="sweet-box-actions">
                          <div class="sweet-action-close">
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24">
                              <path
                                d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z"
                                fill="#292c34"
                              ></path>
                            </svg>
                          </div>
                        </div>
                        <div class="sweet-content">
                          <div class="sweet-content-content">
                            <div class="content-dialog-claim-success">
                              <div class="main-title">CLAIM REWARD SUCCESSFULLY!</div>
                              <!--                                  <img src="@/assets/images/rat_hold_bnb.1d72ec18.png" class="image-logo-token" />-->
                              <div class="reward-content">
                                <div class="text-1">Your reward: 0.0000 BNB</div>
                                <div class="text-2">
                                  Next collectable date: Mon, May 31, 2021 2:04 PM (at your time zone)
                                </div>
                              </div>
                              <div class="button-share-wrapper">
                                <div>Share now</div>
                                <div class="p-1">
                                  <a
                                    page-title="I just collected 0.0000 BNB at MoonRat.Finance! You should give it a try!
                                                         #MoonRat #EarnBNB #BinanceSmartChain #BSC #MKAT"
                                    button-design="flat"
                                    has-icon="true"
                                    class="button-social twitter__design__flat"
                                    ><i class="icon-twitter"></i></a
                                  ><a button-design="flat" has-icon="true" class="button-social facebook__design__flat"
                                    ><i class="icon-facebook"></i
                                  ></a>
                                </div>
                              </div>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="tab-content">
                  <div
                    id="two"
                    class="tab-pane fade p-3"
                    role="tabpanel"
                    aria-labelledby="two-tab"
                    :class="{ 'active show': isActive('two') }"
                  >
                    <Statistic
                      v-if="contract"
                      :hundredthousandmkatusd="hundredThousandMKATUSD"
                      :totalliquiditypoolusd="totalLiquidityPoolUSD"
                      :totalbnbinpool="totalBnbInPool"
                      :contract="contract"
                    />
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div>
      <b-modal id="bv-share-modal" hide-footer>
        <template #modal-title>
          Congratulations!
        </template>
        <div class="d-block text-center">You just withdrawed {{ cakeAvailableReward }}. Wanna share it on twitter?</div>
        <b-button class="mt-3" block>
          <ShareNetwork
            network="twitter"
            url="https://moonkat.net/"
            :title="`I just claimed ${cakeAvailableReward} CAKE only by holding BBC token. You can try it too!`"
            @open="open"
          >
            Of course!
          </ShareNetwork>
        </b-button>
      </b-modal>
    </div>
  </section>
</template>

<script>
import { mapGetters } from "vuex";
import { utils, ethers, BigNumber } from "ethers";

import { CONTRACT_ADDRESS } from "@/constants";
import MetamaskService from "@/MetamaskService";
import Sidebar from "@/components/Dashboard/Sidebar";
import Statistic from "@/components/Dashboard/Statistic";

export default {
  name: "Dashboard",
  components: { Statistic, Sidebar },
  data() {
    return {
      service: null,
      contract: null,
      activeItem: "one",
      maxMkatTx: null,
      hundredThousandMKATUSD: "...",
      isRewardClaimAvailable: false,
      cakeAvailableReward: "0",
      cakeTotalGainedReward: "0",
      nextClaimDate: "0",
      myBnbRewardAfterTax: 0,
      totalBnbInPool: "...",
      estimatedGas: {},
      myMkatBalance: "...",
      totalLiquidityPoolUSD: "...",
      recipientAddress: "",
      amountMkat: 0,
      maxBNBTx: "...",
      provider: null,
      balanceOfAddress: 0,
    };
  },
  computed: {
    ...mapGetters(["signerAddress"]),
    ...mapGetters(["walletProviderType"]),
  },
  watch: {
    cakeAvailableReward() {},
  },
  async mounted() {
    if (!this.signerAddress) {
      console.error("user`s wallet is not connected");
      this.$router.replace({ path: "connect-wallet" });
      return;
    }

   
    try {
      this.$loading(true);

      this.service = new MetamaskService(await MetamaskService.createWalletProviderFromType(this.walletProviderType));


      if(this.service.getCurrentWalletProvider().networkVersion != '56') { 
        if(!(await this.service.switchChainAsync(56))) { 
          console.log("cannot switch chain");
          return;
        }

        this.service = new MetamaskService(await MetamaskService.createWalletProviderFromType(this.walletProviderType));
      }

      const web3Provider =  this.service.getWeb3Provider();
      web3Provider.provider.on("chainChanged", newNetwork => {
          if(parseInt(newNetwork, 16) != 56) { 
            window.location.reload();
          }
      });

      web3Provider.provider.on("accountsChanged", ([newAddres]) => {
          window.location.reload();
      });

      await this.service.initialize();

      await this.loadContractInfo();

      const that = this;
      setInterval(function() {
        that.loadContractInfo();
      }, 10000);
    } catch (ex) {
      console.error(ex);
      alert("An error occured. Error msg: " + ex.message);
    } finally {
      this.$loading(false);
    }
  },

  methods: {
    async loadContractInfo() {
      console.debug("wallet provider: ", this.walletProviderType);
      console.debug("signer address: ", this.signerAddress);

      this.contract = this.service.getTokenContractInstance();
      console.debug("contract:  ", this.contract);

      this.provider = this.service.getWeb3Provider();

      this.balanceOfAddress = utils.formatUnits(await this.contract.balanceOf(this.signerAddress), 18);

      const staticRewardsInfo = await this.contract.getAccountDividendsInfo(this.signerAddress);

      console.debug("static rewards : ", staticRewardsInfo);

      this.isRewardClaimAvailable = staticRewardsInfo[1].gt(BigNumber.from("0"));

      console.debug("is reward claim available for user: ", this.isRewardClaimAvailable);

      if (this.isRewardClaimAvailable == true) {
        this.cakeAvailableReward = utils.formatUnits(staticRewardsInfo[3], 18);
        this.cakeTotalGainedReward = utils.formatUnits(staticRewardsInfo[4], 18);

        this.nextClaimDate = new Date(staticRewardsInfo[6]) / 1000;

        console.debug("available reward: ", this.cakeAvailableReward);
        console.debug("next claim date: ", this.nextClaimDate);
      }

      const hundredThousandMKAT = utils.parseUnits("100000", 18);
      const usdPrice = await this.service.getMkatValueInBUSD(hundredThousandMKAT);

      this.hundredThousandMKATUSD = parseFloat(utils.formatUnits(usdPrice, 18)).toFixed(2);
      this.totalLiquidityPoolUSD = parseFloat(utils.formatEther(await this.service.totalLiquidityPoolInBUSD())).toFixed(
        2
      );

      const totalBnbInLiquidityPool = (await this.service.getPancakePairPoolReserves())[1];
      this.totalBnbInPool = parseFloat(utils.formatEther(totalBnbInLiquidityPool)).toFixed(2);
    },
    isActive(menuItem) {
      return this.activeItem === menuItem;
    },
    setActive(menuItem) {
      this.activeItem = menuItem;
    },

    open() {
      this.$bvModal.hide("bv-share-modal");
    },
    openShareOnTwitterModal() {
      this.$bvModal.show("bv-share-modal");
    },
    async claimMyReward() {
      try {
        this.$loading(true);
        const txResponse = await this.contract.claim();
        const resp = await txResponse.wait();
        console.debug({ txResponse: resp });

        this.openShareOnTwitterModal();
      } catch (ex) {
        console.debug("claimBNB exception: ", ex);
      } finally {
        this.$loading(false);
      }
    },
  },
};
</script>

<style>
@font-face {
  font-family: "SourceSansPro-Semibold";
  src: local("SourceSansPro-Semibold"), url(../assets/fonts/SourceSansPro-Semibold.ttf) format("truetype");
}
</style>

<style>
.dashboard-content {
  padding: 0 10px;
}

.modal-content {
  background-color: black;
  color: white !important;
  -webkit-box-shadow: 3px 3px 46px 31px rgba(9, 9, 9, 0.43);
  -moz-box-shadow: 3px 3px 46px 31px rgba(9, 9, 9, 0.43);
  box-shadow: 3px 3px 46px 31px rgba(9, 9, 9, 0.43);
}
.buyCardBody {
  display: flex;
  align-items: center;
  gap: 5px;
}

.buyCard {
  display: inline-block;
}

.share-network-twitter {
  color: white;
}

.share-network-twitter:hover {
  color: white;
}

.info-container {
  border-radius: 20px;
  background-color: #190053;
}
.social-links-container {
  display: flex;
  align-items: center;
  justify-content: end;
}
.social-links {
  display: flex;
  align-items: center;
  margin: 30px 0 20px;
  gap: 15px;
}
.social-links img {
  height: 23px;
}
.claim-reward-content {
  position: relative;
}

.text-warning {
  position: relative;
  top: 15px;
  color: #ff3907 !important;
}

.nav-item a {
  font-family: SourceSansPro-Semibold;
  font-size: 16px;
  font-weight: 700;
  margin-top: 10px;
}

.el-button.button-send-disruptive.el-button--primary.el-button--medium {
  width: 100%;
  height: 44px;
  display: block;
  line-height: 34px;
  border: none;
}

.el-button.button-send-disruptive.el-button--primary.el-button--medium span {
  color: #c4c6e7;
  font-weight: 500;
  padding-top: 4px;
  font-size: 15px;
}

.el-button.button-send-disruptive.el-button--primary.el-button--medium:hover span {
  transition: color 0.3 linear;
  color: #e6e7ff;
}

.el-input__inner::-webkit-input-placeholder {
  color: #190053 !important;
}
.el-input__inner:-moz-placeholder {
  color: #190053 !important;
}
.el-input__inner::-moz-placeholder {
  color: #190053 !important;
}
.el-input__inner:-ms-input-placeholder {
  color: #190053 !important;
}
</style>
