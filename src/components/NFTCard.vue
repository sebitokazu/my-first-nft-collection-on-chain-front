<template>
  <v-card
    class="mx-auto my-12"
  >
    <v-img
      height="250"
      :src="nft.image"
    ></v-img>

    <v-card-title>{{nft.name}}</v-card-title>

    <v-card-text>
      <p>Owner: {{nft.owner}}</p>
    </v-card-text>

    <v-card-text>
    </v-card-text>
  </v-card>
</template>
<script>
  export default {
      name:"NFTCard",
      props:{
          nft:{
              type: Object
          }
      }
  }
</script>