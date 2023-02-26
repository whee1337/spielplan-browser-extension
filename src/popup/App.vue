<template>
  <div class="row">
    <div class="col-md-12">
      <h3 v-if="!loading" >Spielplan</h3>
    </div>
    <div v-if="loading" style="display: flex; justify-content: center; align-items: center;height:250px">
    <socket></socket>
  </div>
    <div class="col-md-12">
      <ul class="list">
        <li
          v-for="(fact, index) in games"
          :key="index"
          :class="fact.teamName === 'Herren' ? 'herren1' : 'herren2'"
        >
          <a
            v-bind:href="fact.heimSpiel ? fact.gastLink : fact.heimLink"
            target="_blank"
          >
            <span class="title">{{ fact.heim }} - {{ fact.gast }}</span>

            <span class="subtitle">{{
              new Date(fact.date).toLocaleString()
            }}</span></a
          >
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import { defineComponent } from "vue";
import axios from "axios";
import {Socket} from '@dgknca/vue-loading-spinner'

/*
var testdata = [
  {
    date: new Date("2022-09-18T12:00:00.000Z"),
    teamName: "Herren",
    heim: "TTC Königstein 1948",
    heimLink:
      "https://www.mytischtennis.de/clicktt/HeTTV/22-23/ligen/Herren-Verbandsliga-Gr-West/gruppe/414395/mannschaft/2587250/TTC-Koenigstein-1948/spielerbilanzen/rr/",
    gast: "TTC Offheim 1949",
    gastLink:
      "https://www.mytischtennis.de/clicktt/HeTTV/22-23/ligen/Herren-Verbandsliga-Gr-West/gruppe/414395/mannschaft/2587592/TTC-Offheim-1949/spielerbilanzen/rr/",
    endstand: "9:0",
    hasBeenPlayed: true,
    heimSpiel: false,
  },
  {
    date: new Date("2022-09-18T12:00:00.000Z"),
    teamName: "HerrenII",
    heim: "TTC Königstein 1948",
    heimLink:
      "https://www.mytischtennis.de/clicktt/HeTTV/22-23/ligen/Herren-Verbandsliga-Gr-West/gruppe/414395/mannschaft/2587250/TTC-Koenigstein-1948/spielerbilanzen/rr/",
    gast: "TTC Offheim 1949",
    gastLink:
      "https://www.mytischtennis.de/clicktt/HeTTV/22-23/ligen/Herren-Verbandsliga-Gr-West/gruppe/414395/mannschaft/2587592/TTC-Offheim-1949/spielerbilanzen/rr/",
    endstand: "9:0",
    hasBeenPlayed: true,
    heimSpiel: false,
  },
];*/

const server_url  =
  "http://localhost:3500/api/spielplan?t=Herren&t=HerrenII";

export default defineComponent({
  name: "Spielplan-App",
  components:{
    Socket
  },

  data() {
    return {
      games: [],
      loading: false
    };
  },
  methods: {
    async fetchCatFacts() {
      this.loading = true;
      const response = await axios.get(server_url);
      this.games = this.sortByDate(this.filterData(response.data));
      this.loading = false;
     // this.games = testdata;
    },
    async loadMoreFacts() {},
    filterData(data) {
      const currentDate = new Date();

      return Object.values(data).filter((v) => {
        return new Date(v.date) > currentDate;
      });
    },
    sortByDate(data) {
      return Object.values(data).sort((v1, v2) => {
        console.log(new Date(v1.date), new Date(v2.date));
        console.log(new Date(v1.date) > new Date(v2.date));
        return (new Date(v1.date) - new Date(v2.date));
      });
    },
  },
  async mounted() {
    await this.fetchCatFacts();
  },
});
</script>
