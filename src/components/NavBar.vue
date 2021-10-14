<template>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <h1>Epic NFT's</h1>
      </div>

      <v-spacer></v-spacer>


      <v-btn 
        v-for="link in links"
        :key="link.name"
        :href="link.link"
        target="_blank"
        text
      >
        <v-icon>{{link.icon}}</v-icon>
      </v-btn>
    </v-app-bar>
</template>

<script>

export default {
  data: () => ({
    links:[
      { name:"twitter", link:`https://twitter.com/seba_itokazu`, icon:"mdi-twitter"},
      { name:"github", link:"https://github.com/sebitokazu", icon:"mdi-github"}
    ],
  }),
};
</script>