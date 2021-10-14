<template>
  <v-app>
    <NavBar />
    <v-main>
        <v-container class="my-4">
          <v-row justify="center">
            <h1>My First NFT Collection</h1>
          </v-row>
          <v-row justify="center">
              <p class="subtitle-1">Claim your's now</p>
          </v-row>
          <v-row v-if="!isConnected" justify="center" class="py-4">
            <WalletButton v-on:login="login" />
          </v-row>
          <div v-else>
            <v-row justify="center">
              <v-col cols="12" md="4">
                    <v-btn
                      rounded
                      color="primary"
                      dark
                      block
                    >
                      Mint
                      <v-icon right>
                        mdi-rocket-launch
                      </v-icon>
                    </v-btn>
              </v-col>
            </v-row>
          </div>
        </v-container>
    </v-main>
    <Footer />
  </v-app>
</template>

<script>
import Footer from "./components/Footer.vue";
import NavBar from "./components/NavBar.vue";
import WalletButton from "./components/WalletButton.vue";

export default {
  name: 'App',

  components: {
    Footer,
    NavBar,
    WalletButton
  },

  data: () => ({
    links:[
      { name:"twitter", link:`https://twitter.com/seba_itokazu`, icon:"mdi-twitter"},
      { name:"github", link:"https://github.com/sebitokazu", icon:"mdi-github"}
    ],
    OPENSEA_LINK: '',
    TOTAL_MINT_COUNT:50,
    currentAccount: ""
  }),

  mounted(){
    this.checkIfWalletIsConnected();
  },
  methods:{
    async checkIfWalletIsConnected(){
        const { ethereum } = window;

        if (!ethereum) {
            console.log("Make sure you have metamask!");
            return;
        } else {
            console.log("We have the ethereum object", ethereum);
        }

        /*
        * Check if we're authorized to access the user's wallet
        */
        const accounts = await ethereum.request({ method: 'eth_accounts' });

        /*
        * User can have multiple authorized accounts, we grab the first one if its there!
        */
        if (accounts.length !== 0) {
            const account = accounts[0];
            console.log("Found an authorized account:", account);
            this.currentAccount = account;
        } else {
            console.log("No authorized account found");
        }
    },
    login(account){
      this.currentAccount = account;
    }
  },
  computed:{
    isConnected(){
      return this.currentAccount !== null && this.currentAccount !== '';
    }
  }
};
</script>
