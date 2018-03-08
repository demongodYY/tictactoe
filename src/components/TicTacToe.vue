<template>
  <div class="hello">
    <table>
      <tr v-for = "(cellLine, lineIndex) in cells" :key="lineIndex">
        <td class="cell" 
          v-for= "(cell, index) in cellLine" 
          :key="index" 
          @click="handleClick({
            row:lineIndex,
            col:index
            })">
          {{cells[lineIndex][index]}}
        </td>
      </tr>
    </table>
    <div class="endgame">
      <div class="text">
      </div>
    </div>
    
    <div class="buttonDiv">
      <button type="button" @click="reset()">Replay</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      cells: [['','',''],
              ['','',''],
              ['','','']],
      gameover: false,
      testResult:[],
    }
  },
  methods: {
    reset () {
      for (let row = 0; row<this.cells.length; row++) {
        for (let col = 0; col<this.cells[row].length; col++){
          this.$set(this.cells[row], col, '');
        }
      }
      this.gameover = false;
    },
    handleClick(index) {
      if (!this.gameover && this.cells[index.row][index.col] === ''){
        this.turnPiece(index, 'O') && this.turnPiece(this.getAIindex(), 'X');
      }
    },
    turnPiece(index, role) {
      this.$set(this.cells[index.row], index.col, role);
      this.steps -= 1;
      if (this.checkGameOver(role, index)){
        return false;
      };
      return true;
    },
    getAIindex() {
      let result = this.miniMax(this.cells,'X');
      return result.index;
    },
    checkWin(cells, role) {
      let winResult = [
        cells[0][0]+cells[1][1]+cells[2][2],
        cells[0][2]+cells[1][1]+cells[2][0],
        cells[0][0]+cells[0][1]+cells[0][2],
        cells[1][0]+cells[1][1]+cells[1][2],
        cells[2][0]+cells[2][1]+cells[2][2],
        cells[0][0]+cells[1][0]+cells[2][0],
        cells[0][1]+cells[1][1]+cells[2][1],
        cells[0][2]+cells[1][2]+cells[2][2]
      ]

      if (winResult.some((ele) => {return ele === 'XXX' || ele === 'OOO'})){
        return role;
      }
      return false;
    },
    checkTie(cells) {
      // console.log(cells);
      for (let i = 0; i<cells.length; i++) {
        if (cells[i].findIndex((ele) => {return ele === '';}) !== -1){
          return false;
        }
      }
      return 'Tie';
    },
    checkGameOver(role,index) {
      if (this.checkWin(this.cells, role, index)){
        alert(`${role} is win!`);
        this.gameover = true;
        return true;
      } else if (this.checkTie(this.cells)){
        alert('Tie!!!!');
        this.gameover = true;
        return true;
      }
      return false;
    },
    createNewCells(cells){
      let newCells = [];
      cells.forEach(row => {
        let newRow = row.slice();
        newCells.push(newRow);
      });
      return newCells;
    },

    checkScore(cells, role) {
      const status = this.checkWin(cells, role) || this.checkTie(cells);
      switch(status){
        case 'X':
          return 10
        case 'O':
          return -10
        case 'Tie':
          return 0
      }
      return false;
    },

    miniMax(cells, role) {
      let newCells = this.createNewCells(cells);
      let score = this.checkScore(newCells, role);          
      if(score !== false) {
        return {score:score};
      }
      let results = [];
      for (let row = 0 ; row < newCells.length; row++){
        for (let col = 0; col < newCells[row].length; col++) {
          if (newCells[row][col] !== ''){
            continue;
          }
          newCells[row][col] = role;
          let index = {row,col};
          let result = {};
          result.index = index ;
          // console.log(index);
          if(role == 'X') {
            result.score = this.miniMax(newCells, 'O').score;
          } else {
            result.score = this.miniMax(newCells, 'X').score;
          }
          results.push(result);
        }
      }
      let bestMove;
      if(role == 'X' ) {
        let bestScore = -1000;
        for (let i = 0; i< results.length; i++) {
          if (results[i].score > bestScore) {
            bestScore = results[i].score;
            bestMove = i;
          }
        }
      } else {
        let bestScore = 1000;
        for (let i = 0; i< results.length; i++) {
          if (results[i].score < bestScore) {
            bestScore = results[i].score;
            bestMove = i;
          }
        }
      }
      return results[bestMove];
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  td {
    border: 2px solid #333;
    height: 100px;
    width: 100px;
    text-align: center;
    vertical-align: middle;
    font-size: 70px;
    cursor: pointer;
  }

  table {
    border-collapse: collapse;
    position: absolute;
    left: 50%;
    /*the table is 310px so margin-left: -155px to make sure the table is centered*/
    margin-left: -155px;
    top: 100px;
  }

  table tr:first-child td {
    border-top: 0;
  }

  table tr:last-child td {
    border-bottom: 0;
  }

  table tr td:first-child {
    border-left: 0;
  }

  table tr td:last-child {
    border-right: 0;
  }
  .buttonDiv {
    text-align: center;
    margin-top: 30px;
  }
  .buttonDiv button {
    cursor: pointer;
  }
  .endgame {
    display:none;
    position: absolute;
    width: 200px;
    top: 120px;
    background-color: rgba(205,133,63,0.8);
    left: 50%;
    margin-left: -100px;
    padding: 50px 0;
    text-align: center;
    border-radius: 5px;
    color: white;
    font-size: 2em;
  }
</style>
