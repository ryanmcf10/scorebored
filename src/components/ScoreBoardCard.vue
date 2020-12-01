<template>
    <div class="column is-one-quarter">
    <div class="scoreboard card" :id="game.elements[0].attributes.id">
      <div class="card-header">
        {{ game.elements[0].attributes.start_time }}
        {{ game.elements[0].attributes.status }}
      </div>
      <div class="card-content">
        <table class="table is-bordered">
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
              {{ game.elements[1].attributes.name }}
            </td>
            <td>
              {{ game.elements[1].elements[0].attributes.R }}
            </td>
            <td>
              {{ game.elements[1].elements[0].attributes.H }}
            </td>
            <td>
              {{ game.elements[1].elements[0].attributes.E }}
            </td>
          </tr>
          <tr>
            <td class="team home-team">
              {{ game.elements[2].attributes.name }}
            </td>
            <td>
              {{ game.elements[2].elements[0].attributes.R }}
            </td>
            <td>
              {{ game.elements[2].elements[0].attributes.H }}
            </td>
            <td>
              {{ game.elements[2].elements[0].attributes.E }}
            </td>

          </tr>
          </tbody>
        </table>

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
</style>