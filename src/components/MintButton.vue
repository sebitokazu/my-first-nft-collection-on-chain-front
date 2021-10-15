<template>
    <v-btn
    rounded
    color="primary"
    dark
    block
    @click="mint"
    >
        Mint
        <v-icon right>
        mdi-rocket-launch
        </v-icon>
    </v-btn>
</template>

<script>
import { ethers } from "ethers";
import abi  from "../assets/abi.json";
export default {
    name: "MintButton",
    data: () => ({    
    }),
    methods:{
        // async mint(){
        //     let nftTxn = this.callContractMint();
        //     nftTxn
        //         .then((res)=>{
        //             console.log(res);
        //             console.log("Mining...please wait.")
        //             this.$emit('minting');
        //             nftTxn.wait()
        //                 .then(()=>{
        //                     console.log(`Mined with TxnHash: ${nftTxn.hash}`);
        //                     this.$emit('minted',nftTxn.hash);
        //                 })
        //                 .catch((err)=>{
        //                     console.log(err);
        //                 })
        //                 .finally(()=>{

        //                 });
        //         })
        //         .catch((err)=>{
        //             console.log(err);
        //         });
                

        // },
        async mint(){
            try {
                const { ethereum } = window;

                if (ethereum) {
                    const provider = new ethers.providers.Web3Provider(ethereum);
                    const signer = provider.getSigner();
                    const connectedContract = new ethers.Contract(process.env.VUE_APP_CONTRACT_ADDRESS, abi.abi, signer);

                    console.log("Going to pop wallet now to pay gas...");
                    let nftTxn = await connectedContract.makeAnEpicNFT();

                    console.log("Mining...please wait.");
                    this.$emit('minting');
                    await nftTxn.wait();
                    
                    console.log(`Mined, see transaction: https://rinkeby.etherscan.io/tx/${nftTxn.hash}`);
                    this.$emit('minted',nftTxn.hash);

                } else {
                    console.log("Ethereum object doesn't exist!");
                }
            } catch (error) {
                console.log(error);
                this.$emit('error', error);
            }
        }
    }
}
</script>