<template>
  <div id="app">
    <div class="columns is-centered">
      <div class="column is-full">
        <v-date-picker color="green" v-model="date" @dayclick="fetchScoreBoard">
          <template v-slot="{ togglePopover }">
            <div class="field has-addons has-addons-centered">
              <div class="control">
                <input class="input" type="text" readonly :value="date.toLocaleString().split(',')[0]" style="text-align: center;"/>
              </div>
              <div class="control">
                <button
                  class="button is-primary"
                  @click="togglePopover({ placement: 'bottom' })"
                >
                  Change
                </button>
              </div>
            </div>
          </template>
        </v-date-picker>
      </div>
    </div>

    <div class="columns is-multiline is-mobile" v-if="games !== null">
      <score-board-card
        v-for="game in games"
        v-bind:game="game"
        v-bind:key="game.game_id"
      />
    </div>
    <div v-else>
      No games scheduled today...
    </div>
  </div>
</template>

<script>
import convert from "xml-js";
import fetch from "node-fetch";
import ScoreBoardCard from "./components/ScoreBoardCard.vue";

export default {
  name: "App",
  components: {
    ScoreBoardCard,
  },
  data() {
    return {
      date: new Date(),
      games: [],
    };
  },
  methods: {
    fetchScoreBoard() {
      // fetch XML data for the given date from mlb.com

      // get date components as zero-padded strings
      var month = this.date.getUTCMonth() + 1;
      var day = this.date.getUTCDate();
      var year = this.date.getUTCFullYear();

      var url = `http://gd2.mlb.com/components/game/mlb/'year_${year}/month_${month}/day_${day}/scoreboard.xml`;

      fetch(url)
        .then((response) => response.text())
        .then(
          // parse the XML into an array of (messy) JSON object
          (str) => {
            try {
              // will throw an error if XML does not contain any game data
              return JSON.parse(convert.xml2json(str)).elements[0].elements.map(
                (element) => element.elements
              );
            } catch {
              return [];
            }
          }
        )
        .then(this.setGames);
    },
    setGames(rawData) {
      if (rawData.length > 0) {
        this.games = rawData.map(this.mapRawGameDataToScoreBoardObjects);
      } else {
        this.games = null;
      }
    },
    mapRawGameDataToScoreBoardObjects(rawGameData) {
      console.log(rawGameData);

      var scoreBoard = {
        game_id: rawGameData[0].attributes.id,
        status: rawGameData[0].attributes.status,
        start_time: rawGameData[0].attributes.start_time,
        home: {
          name: rawGameData[1].attributes.name,
          runs: rawGameData[1].elements[0].attributes.R,
          hits: rawGameData[1].elements[0].attributes.H,
          errors: rawGameData[1].elements[0].attributes.E,
        },
        away: {
          name: rawGameData[2].attributes.name,
          runs: rawGameData[2].elements[0].attributes.R,
          hits: rawGameData[2].elements[0].attributes.H,
          errors: rawGameData[2].elements[0].attributes.E,
        },
      };

      console.log(`Game Data length: ${rawGameData.length}`);
      if (rawGameData.length == 6) {
        // game is over
        scoreBoard["winning_pitcher"] = {
          name: rawGameData[3].elements[0].attributes.name,
          wins: rawGameData[3].attributes.wins,
          losses: rawGameData[3].attributes.losses,
        };

        scoreBoard["losing_pitcher"] = {
          name: rawGameData[4].elements[0].attributes.name,
          wins: rawGameData[4].attributes.wins,
          losses: rawGameData[4].attributes.losses,
        };

        if (rawGameData[5].elements[0].attributes.name != ". ") {
          scoreBoard["save_pitcher"] = {
            name: rawGameData[5].elements[0].attributes.name,
            saves: rawGameData[5].attributes.saves
          };
        } else {
          scoreBoard["save_pitcher"] = null;
        }
        
      } else {
        // game hasn't been played
        scoreBoard["winning_pitcher"] = null;
        scoreBoard["losing_pitcher"] = null;
      }

      //console.log(scoreBoard);
      return scoreBoard;
    },
  },
  mounted() {
    this.fetchScoreBoard();
  },
};
</script>

<style>
#app {
  font-family: monospace;
  color: white;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
</style>
