<template>
    <v-btn
        class="ma-2"
        :loading="loading"
        :disabled="loading"
        color="secondary"
        @click="connectToWallet"
    >
        Connect to wallet
        <v-icon right>mdi-wallet</v-icon>
    </v-btn>
</template>

<script>
export default {
    name: "WalletButton",
    data: () => ({
        loader: null,
        loading: false,       
    }),
    methods:{
        async connectWallet(){
            const { ethereum } = window;
            if(!ethereum){
                alert("Get Metamask");
                throw Error;
            }

            return await ethereum.request({ method: "eth_requestAccounts" });


        },
        connectToWallet(){
            this.loader = 'loading';
            this.loading = true;

            this.connectWallet()
                .then((res)=>{
                    console.log("Connected", res[0]);
                    this.$emit('login',res[0]);
                })
                .catch((err)=>{
                    console.log(err);
                })
                .finally(()=>{
                    this.loader = null;
                    this.loading = false;
                })
        }
    }
}
</script>