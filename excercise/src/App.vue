<template>
  <v-app>
    <NineHoleView />
  </v-app>
</template>

<script>
import NineHoleView from "./views/NineHoleView.vue";
export default {
  data() {
    return {
      shotData: {},
      shots: [],
    };
  },
  mounted() {
    //* Sample Vuex call to get shot data.
    //* You can alter this as well if you see a better way to handle this action.
    this.$store.dispatch("shots/allShots");
  },
  components: { NineHoleView },
};
</script>

<style></style>
