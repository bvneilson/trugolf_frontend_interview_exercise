<template>
  <v-dialog
    transition="dialog-bottom-transition"
    max-width="600"
    v-model="showModal"
  >
    <template>
      <!-- Ran out of time to style/format this modal, would love to make customizable data display here and show cool stuff on the graph -->
      <v-card>
        <v-toolbar color="primary" dark>Shot details</v-toolbar>
        <v-card-text>
          <div class="text-h5 pa-12">
            <strong>Club used: </strong>{{ shot.clubTypeKey }}
          </div>
          <div class="text-h5 pa-12">
            <strong>Shot time: </strong>{{ shot.createdAt }}
          </div>
          <!-- This value does not work perfectly, bugs out with wedges and irons - would like to fine tune and add more useful data points and work on styling - fix edge cases dealing with undefined and when a club only has one data point -->
          <v-sparkline
            :value="getSimilarShotValues()"
            :gradient="gradient"
            :smooth="radius || false"
            :padding="padding"
            :line-width="width"
            :stroke-linecap="lineCap"
            :gradient-direction="gradientDirection"
            :fill="fill"
            :type="type"
            :auto-line-width="autoLineWidth"
            auto-draw
          ></v-sparkline>
        </v-card-text>
        <v-card-actions class="justify-end">
          <v-btn text @click="showModal = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </template>
  </v-dialog>
</template>

<script>
const gradients = [
  ["#222"],
  ["#42b3f4"],
  ["red", "orange", "yellow"],
  ["purple", "violet"],
  ["#00c6ff", "#F0F", "#FF0"],
  ["#f72047", "#ffd200", "#1feaea"],
];

export default {
  props: ["shotModal", "shot", "similarShots"],

  data() {
    return {
      width: 2,
      radius: 10,
      padding: 8,
      lineCap: "round",
      gradient: gradients[5],
      gradientDirection: "top",
      gradients,
      fill: false,
      type: "trend",
      autoLineWidth: false,
    };
  },

  computed: {
    showModal: {
      get() {
        return this.shotModal;
      },
      set(shotModal) {
        this.$emit("closeShotModal", shotModal);
      },
    },
  },

  methods: {
    getSimilarShotValues() {
      return this.similarShots.map((similarShot) => {
        if (similarShot.carryDistance !== undefined) {
          return similarShot.carryDistance;
        }
      });
    },
  },
};
</script>
