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
      <button type="button" onClick="startGame()">Replay</button>
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
    }
  },
  methods: {
    handleClick(index) {
      if (this.cells[index.row][index.col] === ''){
        this.turnPiece(index, 'X');
        this.turnPiece(this.getAIindex(), 'O');
      }
    },
    turnPiece(index, role) {
      this.$set(this.cells[index.row], index.col, role);
    },
    getAIindex() {
      let index = {
        col: -1,
        row: 0
      };
      for (let i = 0; i<this.cells.length; i++) {
          index.col = this.cells[i].findIndex((ele) => {
            return ele === '';
          });
          if (index.col !== -1) {
            index.row = i;
            break;
          }
      }
      return index
    },
    checkWin(role ,index) {
      let resultRow = '';
      let resultCol = '';
      let resultDigLeft = '';
      let resultDigRight = ''; 
      for (let i =0; i<this.cells.length; i++) {
        resultRow += this.cells[index.row][i];
        resultCol += this.cells[i][index.col];
      }
      resultDigLeft = this.cells[0][0]+this.cells[1][1]+this.cells[2][2]
      resultDigRight = this.cells[0][2]+this.cells[1][1]+this.cells[2][0]
      if(resultRow == resultCol == resultDigLeft == resultDigRight == 'XXX' || resultRow == resultCol == resultDigLeft == resultDigRight == 'OOO'){
        return role;
      }
      return '';
    },
    checkTie() {

    },
    checkGameOver() {

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
