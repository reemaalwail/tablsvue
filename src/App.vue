<template>
  <v-app>
    <v-content>
      <TablesDetail />
    </v-content>
  </v-app>
</template>

<script>
import TablesDetail from "./components/TablesDetail.vue";

export default {
  name: "App",

  components: {
    TablesDetail
  },

  data: () => ({
    //
  })
};
</script>
