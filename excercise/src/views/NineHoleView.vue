<template>
  <v-container>
    <v-row>
      <h1>9-hole round shots</h1>
    </v-row>
    <v-row>
      <RoundShots />
    </v-row>
  </v-container>
</template>

<script>
import RoundShots from "../components/RoundShots.vue";

export default {
  components: {
    RoundShots,
  },
};
</script>
