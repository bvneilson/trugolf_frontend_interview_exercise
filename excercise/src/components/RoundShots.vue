<template>
  <div>
    <v-btn color="primary" class="ma-2" dark @click="headerDialog = true">
      Modify viewable data
    </v-btn>
    <!-- I'd like this to be v-data-table, utilizing slots for advanced column filtering -->
    <v-simple-table class="elevation-1">
      <template v-slot:default>
        <thead>
          <tr>
            <th v-for="header in activeHeaders" class="text-left">
              {{ header.name }}
            </th>
          </tr>
        </thead>
        <tbody>
          <!-- iterating through shots array, v-showing if shot club type is found in selected headers -->
          <tr
            v-for="(shot, index) in shots"
            v-show="
              modifiedClubFilters.length > 0
                ? modifiedClubFilters.includes(shot.clubTypeKey)
                : true
            "
            :key="index"
            @click="showShotModal(shot)"
          >
            <td v-for="header in activeHeaders">{{ shot[header.value] }}</td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
    <!-- Decided to put club filters and header filters under same "modify viewable data" umbrella. May lend itself better to an admin setting but was thinking this would be useful/fun to me as a user who loves analytics maybe with slight adaptation -->
    <v-dialog v-model="headerDialog" max-width="500px">
      <v-card>
        <v-card-title> Modify Viewable Data </v-card-title>
        <v-card-text>
          <v-select
            v-model="modifiedClubFilters"
            :items="clubFilters"
            label="Clubs"
            multiple
            chips
          ></v-select>
          <v-select
            v-model="activeHeaders"
            :items="headers"
            item-text="name"
            item-value="value"
            label="Table Headers"
            multiple
            chips
            return-object
          ></v-select>
        </v-card-text>
        <v-card-actions>
          <v-btn color="primary" text @click="headerDialog = false">
            Close
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <ShotModal
      :shotModal="shotModal"
      :shot="selectedShot"
      :similarShots="similarShots"
      @closeShotModal="shotModal = $event"
    />
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import ShotModal from "./ShotModal.vue";

export default {
  data() {
    return {
      headers: [],
      // These are the starting headers to be shown when the user arrives at the page
      activeHeaders: [
        { name: "Course Hole Index", value: "courseHoleIndex" },
        { name: "Shot Index", value: "shotIndex" },
        { name: "Club Type Key", value: "clubTypeKey" },
        { name: "Total Distance", value: "totalDistance" },
        { name: "Carry Distance", value: "carryDistance" },
        { name: "Launch Ball Speed", value: "launchBallSpeed" },
        { name: "Launch Back Spin", value: "launchBackSpin" },
        { name: "Launch Angle", value: "launchAngle" },
      ],
      headerDialog: false,
      // This and other data I'd love to spend more time formatting for readability, understanding etc. (i.e. 1Wood = Driver)
      clubFilters: [
        "1Wood",
        "3Wood",
        "5Wood",
        "4Hybrid",
        "6Iron",
        "7Iron",
        "9Iron",
        "PitchingWedge",
        "SandWedge",
        "Putter",
      ],
      modifiedClubFilters: [],
      selectedShot: {},
      shotModal: false,
      similarShots: [],
    };
  },

  methods: {
    showShotModal(shot) {
      // Setting single shot and similar shots (to make a graph) to pass to the single shot modal
      this.selectedShot = shot;
      this.similarShots = this.shots.filter((shot) => {
        return shot.clubTypeKey === this.selectedShot.clubTypeKey;
      });
      this.shotModal = true;
    },
  },

  computed: {
    ...mapGetters({ shots: "shots/shotList" }),
  },

  watch: {
    shots(shots) {
      let keys = Object.keys(shots[0]);
      keys.forEach((key) => {
        this.headers.push({
          // Not a perfect regex - "ID" gets split up to "I D", would like to fine tune
          name: key.replace(/([A-Z])/g, " $1").replace(/^./, function (str) {
            return str.toUpperCase();
          }),
          value: key,
        });
      });
    },
  },

  components: {
    ShotModal,
  },
};
</script>
