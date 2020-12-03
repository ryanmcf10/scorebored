
<template>
  <div id="app">
    <div class="columns is-multiline is-mobile">
      <score-board-card
        v-for="game in games"
        v-bind:game="game"
        v-bind:key="game.elements[0].id"
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
    fetchScoreBoard() {
      var date = new Date('2020-09-01');

      // get date components as zero-padded strings
      var month = (date.getUTCMonth() + 1);
      var day = date.getUTCDate();
      var year = date.getUTCFullYear();

      var url = `http://gd2.mlb.com/components/game/mlb/'year_${year}/month_${month}/day_${day}/scoreboard.xml`;
      let data = {};

      fetch(url).then(response => response.text()).then(str => {
          data = JSON.parse(convert.xml2json(str));
      }).then(() => {
          this.games = data.elements[0].elements;
          console.log(data.elements[0].elements);
      });
    }
  },
  mounted() {
    this.fetchScoreBoard();
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