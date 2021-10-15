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
                <MintButton v-on:minted="confirmMint" v-on:error="mintError"/>
              </v-col>
            </v-row>
            <v-row>
              <p class="text-h6">Total NFT minted: {{mintCount}}/{{TOTAL_MINT_COUNT}}</p>
           </v-row>
           <v-row>
             <p>Last NFT minted: </p>
           </v-row>
          </div>
        </v-container>
        <v-snackbar
        v-model="snackbar"
        :timeout="timeout"
        >
          {{ snackbarText }}
        <template v-slot:action="{ attrs }">
          <v-btn
            color="red"
            text
            v-bind="attrs"
            @click="snackbar = false"
          >
            Close
          </v-btn>
        </template>
      </v-snackbar>
      <v-snackbar
        v-model="mintedNftSnackbar"
        :timeout="timeout2"
        bottom
        right
        color="green"
        :multi-line="true"
        >
          {{ mintedNftSnackbarText }}
        <template v-slot:action="{ attrs }">
          <v-btn
            color="red"
            text
            v-bind="attrs"
            @click="mintedNftSnackbarText = false"
          >
            Close
          </v-btn>
        </template>
      </v-snackbar>      
    </v-main>
    <Footer />
  </v-app>
</template>

<script>
import Footer from "./components/Footer.vue";
import NavBar from "./components/NavBar.vue";
import WalletButton from "./components/WalletButton.vue";
import MintButton from "./components/MintButton.vue";
import { ethers } from "ethers";
import abi from "./assets/abi.json";

export default {
  name: 'App',

  components: {
    Footer,
    NavBar,
    WalletButton,
    MintButton
  },

  data: () => ({
    links:[
      { name:"twitter", link:`https://twitter.com/seba_itokazu`, icon:"mdi-twitter"},
      { name:"github", link:"https://github.com/sebitokazu", icon:"mdi-github"}
    ],
    OPENSEA_LINK: 'https://testnets.opensea.io/assets',
    TOTAL_MINT_COUNT:100,
    mintCount:0,
    currentAccount: "",
    snackbar:false,
    timeout:2500,
    snackbarText:"",
    mintedNftSnackbar: false,
    mintedNftSnackbarText:"",
    timeout2:3000,
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
            this.getTotalMintCount();
        } else {
            console.log("No authorized account found");
        }
    },
      // Setup our listener.
    setupEventListener(){
      // Most of this looks the same as our function askContractToMintNft
      try {
        const { ethereum } = window;

        if (ethereum) {
          // Same stuff again
          const provider = new ethers.providers.Web3Provider(ethereum);
          const signer = provider.getSigner();
          const connectedContract = new ethers.Contract(process.env.VUE_APP_CONTRACT_ADDRESS, abi.abi, signer);

          // THIS IS THE MAGIC SAUCE.
          // This will essentially "capture" our event when our contract throws it.
          // If you're familiar with webhooks, it's very similar to that!
          connectedContract.on("NewEpicNFTMinted", (from, tokenId) => {
            console.log(from, tokenId.toNumber());
            this.mintCount+=1;
            this.mintedNftSnackbar = true;
            this.mintedNftSnackbarText = `NFT ID: ${tokenId.toNumber()} was minted. It may be blank right now. It can take a max of 10 min to show up on OpenSea. Here's the link: ${this.OPENSEA_LINK}/${process.env.VUE_APP_CONTRACT_ADDRESS}/${tokenId.toNumber()}`;
          });

          console.log("Setup event listener!")

        } else {
          console.log("Ethereum object doesn't exist!");
        }
      } catch (error) {
        console.log(error)
      }
   },
    login(account){
      this.currentAccount = account;
      this.getTotalMintCount();
    },
    confirmMint(txnHash){
      this.timeout = null;
      this.snackbarText = `Mined, see transaction: https://rinkeby.etherscan.io/tx/${txnHash}`;
      this.snackbar = true;
    },
    mintError(error){
      this.timeout = 2500;
      this.snackbarText = `An error during mining, ${error.message}`;
      this.snackbar = true;
    },

    async getTotalMintCount(){
      try {
        const { ethereum } = window;

        if (ethereum) {
          // Same stuff again
          const provider = new ethers.providers.Web3Provider(ethereum);
          const signer = provider.getSigner();
          const connectedContract = new ethers.Contract(process.env.VUE_APP_CONTRACT_ADDRESS, abi.abi, signer);

          
          let maxSupply = await connectedContract.MAX_SUPPLY();

          this.TOTAL_MINT_COUNT = maxSupply;

        } else {
          console.log("Ethereum object doesn't exist!");
        }
      } catch (error) {
        console.log(error)
      }
    },
    async getCurrentMintCount(){
      try {
        const { ethereum } = window;

        if (ethereum) {
          // Same stuff again
          const provider = new ethers.providers.Web3Provider(ethereum);
          const signer = provider.getSigner();
          const connectedContract = new ethers.Contract(process.env.VUE_APP_CONTRACT_ADDRESS, abi.abi, signer);

          
          let count = await connectedContract.getTotalNFTsMinted();

          this.mintCount = count;

        } else {
          console.log("Ethereum object doesn't exist!");
        }
      } catch (error) {
        console.log(error)
      }
    }
  },
  computed:{
    isConnected(){
      return this.currentAccount !== null && this.currentAccount !== '';
    }
  }
};
</script>
