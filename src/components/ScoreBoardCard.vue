<template>
    <div class="column is-one-quarter">
    <div class="scoreboard card" :id="game.game_id">
      <div class="card-content">
        <div class="info">
          <p>First Pitch: {{ game.start_time }}</p>
          <p>{{ game.status }}</p>

        </div>

        <table class="table is-bordered is-fullwidth">
          <thead>
            <tr>
              <td></td>
              <td>R</td>
              <td>H</td>
              <td>E</td>
            </tr>
          </thead>
          <tbody>

          <tr>
            <td class="team away-team">
              {{ game.away.name }}
            </td>
            <td>
              {{ game.away.runs }}
            </td>
            <td>
              {{ game.away. hits }}
            </td>
            <td>
              {{ game.away. errors }}
            </td>
          </tr>
          <tr>
            <td class="team home-team">
              {{ game.home.name }}
            </td>
            <td>
              {{ game.home.runs }}
            </td>
            <td>
              {{ game.home.hits }}
            </td>
            <td>
              {{ game.home.errors }}
            </td>

          </tr>
          </tbody>
        </table>

        <div class="pitchers" v-if="game.winning_pitcher !== null && game.losing_pitcher !== null">
          <div class="pitcher">
            <p><strong class="has-text-white">W: </strong>{{ game.winning_pitcher.name }} [ {{game.winning_pitcher.wins }}-{{ game.winning_pitcher.losses }} ]</p>
          </div>
          <div class="pitcher">
            <p><strong class="has-text-white">L: </strong>{{ game.losing_pitcher.name }} [ {{game.losing_pitcher.wins }}-{{ game.losing_pitcher.losses }} ]</p>
          </div>
          <div class="pitcher" v-if="game.save_pitcher !== null">
            <p><strong class="has-text-white">S: </strong>{{ game.save_pitcher.name }} [ {{game.save_pitcher.saves }} ]</p>
          </div>

        </div>

      </div>
    </div>
    </div>
  </template>

<script>
export default {
  name: 'ScoreBoardCard',
  props: {
    game: Object
  }
}
</script>

<style scoped>
.team {
  text-align: left;
}

.info, .pitchers {
  font-size: .75em;
}

.info {
  text-align: left;
}

.pitchers {
  text-align: right;
}

table {
  margin: .5em 0 !important;
}
</style>