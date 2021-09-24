<template>
  <div>
    <!-- <Banner />
    <Features />
    <Tokenomics />
    <RoadMap />
    <Team />
    <Media />
    <Contacts />
    <Footer /> -->
  </div>
</template>

<script>
import Banner from "@/components/Banner";
import Features from "@/components/Features";
import Tokenomics from "@/components/Tokenomics";
import RoadMap from "@/components/RoadMap";
import Team from "@/components/Team";
import Media from "@/components/Media";
import Contacts from "@/components/Contacts";
import Footer from "@/components/Footer";
import { mapGetters } from "vuex";
import router from '../routes';

export default {
  name: "Home",
  components: {
    // Footer,
    // Contacts,
    // Media,
    // Team,
    // RoadMap,
    // Tokenomics,
    // Features,
    // Banner,
  },
  mounted() {
    router.replace({path: "/dashboard"});
    // console.log(this.signer, this.signerAddress, this.contract);
  },
};
</script>

<style scoped></style>
