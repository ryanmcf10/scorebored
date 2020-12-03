
<template>
  <div id="app">
    <div class="columns is-multiline is-mobile">
      <score-board-card
        v-for="game in games"
        v-bind:game="game"
        v-bind:key="game.game_id"
      />
    </div>
  </div>
</template>

<script>
import ScoreBoardCard from './components/ScoreBoardCard.vue'
import convert from 'xml-js'
import fetch from 'node-fetch'

export default {
  name: 'App',
  components: {
    ScoreBoardCard
  },
  data() {
    return {
      games: []
    }
  },
  methods: {
    fetchScoreBoard(date) {
      // fetch XML data for the given date from mlb.com

      // get date components as zero-padded strings
      var month = (date.getUTCMonth() + 1);
      var day = date.getUTCDate();
      var year = date.getUTCFullYear();

      var url = `http://gd2.mlb.com/components/game/mlb/'year_${year}/month_${month}/day_${day}/scoreboard.xml`;

      fetch(url).then(
        response => response.text()
      ).then(
        // parse the XML into an array of (messy) JSON object
        str => {
          return JSON.parse(convert.xml2json(str)).elements[0].elements.map(element => element.elements);
        }
      ).then(
        this.setGames
      );
    },
    setGames(rawData) {
      this.games = rawData.map(this.mapRawGameDataToScoreBoardObjects);
    },
    mapRawGameDataToScoreBoardObjects(rawGameData){
      console.log(rawGameData);

      var scoreBoard = {
        'game_id': rawGameData[0].attributes.id,
        'status': rawGameData[0].attributes.status,
        'start_time': rawGameData[0].attributes.start_time,
        'away': {
          'name': rawGameData[1].attributes.name,
          'runs': rawGameData[1].elements[0].attributes.R,
          'hits': rawGameData[1].elements[0].attributes.H,
          'errors': rawGameData[1].elements[0].attributes.E,
        },
        'home': {
          'name': rawGameData[2].attributes.name,
          'runs': rawGameData[2].elements[0].attributes.R,
          'hits': rawGameData[2].elements[0].attributes.H,
          'errors': rawGameData[2].elements[0].attributes.E,
        }
      };

      console.log(`Game Data length: ${rawGameData.length}`);
      if (rawGameData.length == 6) {
        // game is over
        scoreBoard['winning_pitcher'] = {
          'name': rawGameData[3].elements[0].attributes.name,
          'wins': rawGameData[3].attributes.wins,
          'losses': rawGameData[3].attributes.losses,
        };

        scoreBoard['losing_pitcher'] = {
          'name': rawGameData[4].elements[0].attributes.name,
          'wins': rawGameData[4].attributes.wins,
          'losses': rawGameData[4].attributes.losses,
        };
      } else {
        // game hasn't been played
        scoreBoard['winning_pitcher'] = null;
        scoreBoard['losing_pitcher'] = null;
      }
      
      //console.log(scoreBoard);
      return scoreBoard;
    }
  },
  mounted() {
    this.fetchScoreBoard(new Date('2020-09-01'));
  }
}
</script>

<style>
#app {
  font-family: monospace;;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 60px;
}
</style>