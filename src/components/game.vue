<template>
  <div>
    <div class="game-board">
      <table cellspacing="0">
        <tr v-bind:key="rowIndex" v-for="(row, rowIndex) in game">
          <td 
            v-bind:key="columnIndex" 
            v-for="(column, columnIndex) in row" 
            v-on:click="click(rowIndex, columnIndex)"
          >
            <transition name="fade">
              <span v-if="game[rowIndex][columnIndex]">
                {{ game[rowIndex][columnIndex] }}
              </span>
            </transition>
          </td>
        </tr>
      </table>
      <p v-if="winner">Game is over! Winner is {{ winner }}</p>
      <button 
        class="reset-button" 
        v-if="emptyCellCount() !== 9" 
        v-on:click="reset"
      >
        RESTART
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'game',
  methods: {
    isDone: function isDone() {
      for (let i = 0; i < 3; i++) {
        if (this.game[i][0] && this.game[i][0] === this.game[i][1] && 
          this.game[i][0] === this.game[i][2]) {
          return true;
        } 

        if (this.game[0][i] && this.game[0][i] === this.game[1][i] && 
          this.game[0][i] === this.game[2][i]) {
          return true;
        } 
      }
      
      if (this.game[0][0] && 
        this.game[0][0] === this.game[1][1] && 
        this.game[0][0] === this.game[2][2]) {
        return true;
      }

      if (this.game[0][2] && 
        this.game[0][2] === this.game[1][1] && 
        this.game[0][2] === this.game[2][0]) {
        return true;
      }
     
      return false;
    },
    reset: function reset() {
      this.turn = 'X';
      this.winner = null;
      this.game = [['', '', ''], ['', '', ''], ['', '', '']];
    },
    moveComputer: function moveComputer() {
      let randomX;
      let randomY;
      do {
        randomX = Math.floor(Math.random() * 3);
        randomY = Math.floor(Math.random() * 3);
      } while (this.game[randomX][randomY] !== '');

      this.game[randomX][randomY] = 'O';

      this.turn = 'X';
    }, 
    emptyCellCount: function emptyCellCount() {
      return this.game.reduce((total, row) => 
        total + row.reduce((rowSum, cell) => 
          cell === '' ? rowSum + 1 : rowSum, 0
        ), 0);
    },
    click: function click(x, y) {
      if (this.winner || this.game[x][y] || this.turn === 'O') {
        return;
      }

      this.game[x][y] = 'X';
      this.turn = 'O';

      if (this.isDone()) {
        this.winner = 'X';
      } 

      else if (this.emptyCellCount() === 0) {
        this.winner = '*dostluk*';
      } 
      else { 
        setTimeout(() => {
          this.moveComputer();

          if (this.isDone()) {
            this.winner = 'O';
          }
          this.$forceUpdate();
        }, 1500);
      } 
      
      this.$forceUpdate();
    },
  },
  data() {
    return {
      turn: 'X',
      winner: null,
      game: [
        ['', '', ''],
        ['', '', ''],
        ['', '', ''],
      ]
    };
  },
};
</script>

<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.game-board table {
  margin: 0 auto;
}

.game-board td {
  font-size: 30px;
  width: 60px;
  height: 60px;
  line-height: 60px;
  text-align: center;
}

.game-board tr:first-child td,
.game-board tr:nth-child(2) td {
  border-bottom: 1px solid #ccc;
}

.game-board tr td:first-child,
.game-board tr td:nth-child(2) {
  border-right: 1px solid #ccc;
}

.reset-button {
  border: none;
  background-color: pink;
  padding: 10px;
  margin-top: 20px;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
