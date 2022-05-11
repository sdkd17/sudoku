<template>
  <div id="main-c"> 
    <div id="header-c" class="grid-item"> 
      <h1> Sudoku </h1>
      <h3> by Gersio </h3>
    </div>
    <div id="right-menu-c" class="grid-item"> 
      <div id="buttons-c">
        <button @click.prevent="nuevoTablero()"> Nuevo </button>
        <button @click.prevent="resetTablero()"> Reset </button>
        <button @click.prevent="mostrarSolucion()"> Solucion </button>
        <button @click.prevent="cambiarAyuda()"> {{botonAyuda}} </button>
        <button @click.prevent="salvarCargarTablero()"> {{ botonSalvar }} </button>
        <button> Restaurar </button>
        <p v-show="salvado"> Tablero Guardado </p>
      </div>      
    </div>
    <div id="board-c" class="grid-item"> 
      <TableroContainer 
        :celdas="puzzle"
        @left-click="handleLeftClick"
        @right-click="handleRightClick"
      />
    </div>
    <div id="footer" class="grid-item"> 
      <span> Sergio Klein - 2022</span>
    </div>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import TableroContainer from '@/components/TableroContainer.vue'

export default {
  name: 'App',
  components: {
    TableroContainer,    
  },
  data(){
    return {
      puzzle: [],
      puzzleString: '',
      solucion: [],
      tableroSalvado: {'puzzle': [], 'columnasRojas': [], 'filasRojas': [], 'cuadradosRojas': []},
      salvado: false,
      rows: 9,
      columnasRojas: [],
      filasRojas: [],
      cuadradosRojas: [],
      ayuda: true,
    }
  },
  mounted() {
    
    this.puzzleString = "...465......2..7..9....76..6....234..15...2.9.4...8........6..17.1...9.3..9...5.."
    let puzzleAPI = this.puzzleString.split("")
    this.puzzle = puzzleAPI.map( e => {
      if (e != '.') {
        return {'nro':parseInt(e), 'clickable': false, 'rojo': false}
      } else{
        return {'nro': 0, 'clickable': true, 'rojo': false}
      }
    })
    
  },
  computed:{
    botonAyuda(){
      if (this.ayuda)  
        return 'Quitar Ayuda' 
      else 
        return 'Ayuda'
    },
    botonSalvar(){
      if (!this.salvado)
        return 'Salvar'
      else  
        return 'Cargar'
    },
    squares(){
      let rtn = []
      for(let i=0;i<this.rows; i++){
        rtn.push([])
      }
      for(let i=0;i<this.rows*this.rows; i++){
        let col = Math.trunc(i/this.rows)
        let row = i %this.rows
        if       ( 0 <= col && col < 3 && 0 <= row && row < 3 ) {
          rtn[0].push(i)
        }else if ( 3 <= col && col < 6 && 0 <= row && row < 3 ){
          rtn[1].push(i)
        }else if ( 6 <= col && col < 9 && 0 <= row && row < 3 ){
          rtn[2].push(i)
        }else if ( 0 <= col && col < 3 && 3 <= row && row < 6 ){
          rtn[3].push(i)
        }else if ( 3 <= col && col < 6 && 3 <= row && row < 6 ){
          rtn[4].push(i)
        }else if ( 6 <= col && col < 9 && 3 <= row && row < 6 ){
          rtn[5].push(i)
        }else if ( 0 <= col && col < 3 && 6 <= row && row < 9 ){
          rtn[6].push(i)
        }else if ( 3 <= col && col < 6 && 6 <= row && row < 9 ){
          rtn[7].push(i)
        }else if ( 6 <= col && col < 9 && 6 <= row && row < 9 ){
          rtn[8].push(i)
        }
      }
      
      return rtn
    }
  },
  methods: {
    nuevoTablero(){
      const options = {
        method: 'GET',
        headers: {
          'X-RapidAPI-Host': '',
          'X-RapidAPI-Key': ''
        }
      };

      fetch('https://sudoku-generator1.p.rapidapi.com/sudoku/generate', options)
        .then(response => response.json())
        .then(response => {
          let puzzleAPI = response.puzzle.split("")
          this.puzzle = puzzleAPI.map( e => {
            if (e != '.') {
              return {'nro':parseInt(e), 'clickable': false, 'rojo': false}
            } else{
              return {'nro': 0, 'clickable': true, 'rojo': false}
            }
          })
        })
        .catch(err => console.error(err));
    },
    mostrarSolucion(){
      if (this.solucion.length === 0){

        const options = {
          method: 'GET',
          headers: {
            'X-RapidAPI-Host': '',
            'X-RapidAPI-Key': ''
          }
        };

        fetch(`https://sudoku-generator1.p.rapidapi.com/sudoku/solve?puzzle=${this.puzzleString}`, options)
          .then(response => response.json())
          .then(response =>{
            this.solucion = response.solution.split("")
            this.puzzle.forEach( (celda, index) => {
              celda.nro = this.solucion[index]
              celda.rojo = false
            })
          })
          .catch(err => console.error(err));
      } else {
      this.puzzle.forEach( (celda, index) => {
        celda.nro = this.solucion[index]
        celda.rojo = false
      })
    }
      
      
    
    },
    resetTablero(){
      this.puzzle.forEach(celda => {
        if(celda.clickable){
          celda.nro = 0
        }
        celda.rojo = false
      })
      this.columnasRojas = []
      this.filasRojas = []
      this.cuadradosRojas = []
    },
    salvarCargarTablero(){
      if (!this.salvado) {
        this.tableroSalvado.puzzle = this.puzzle.map( celda => {
          return {'nro': celda.nro, 'clickable': celda.clickable, 'rojo': celda.rojo}
        })
        this.tableroSalvado.columnasRojas = this.columnasRojas.slice(0)
        this.tableroSalvado.filasRojas = this.filasRojas.slice(0)
        this.tableroSalvado.cuadradosRojas = this.cuadradosRojas.slice(0)
      }else{ //cargar
        this.puzzle = this.tableroSalvado.puzzle
        this.columnasRojas = this.tableroSalvado.columnasRojas
        this.filasRojas = this.tableroSalvado.filasRojas
        this.cuadradosRojas = this.tableroSalvado.cuadradosRojas
      }  
      this.salvado = !this.salvado
    },
    cambiarAyuda(){
      if (this.ayuda){
        this.puzzle.forEach(celda => celda.rojo = false)
      }else {
        this.pintar();
      }
      this.ayuda = !this.ayuda
    },
    handleLeftClick(nroCelda){
      if (this.puzzle[nroCelda].clickable) {
        this.puzzle[nroCelda].nro = (this.puzzle[nroCelda].nro + 1) % (this.rows+1)
        if (this.ayuda) {
          this.checkSquare(nroCelda)
          this.checkRow(nroCelda)
          this.checkColumn(nroCelda)
          this.pintar()
        }
      }
    },
    handleRightClick(nroCelda){
      if (this.puzzle[nroCelda].clickable) {
        this.puzzle[nroCelda].nro = (this.puzzle[nroCelda].nro + this.rows) % (this.rows+1) 
        if (this.ayuda) {
          this.checkSquare(nroCelda)
          this.checkRow(nroCelda)
          this.checkColumn(nroCelda)
          this.pintar()
        }
      }
    },
    checkRow(nroCelda) {

      let nroRow = Math.trunc(nroCelda/this.rows)
      
      let row = this.puzzle.slice(nroRow*this.rows , this.rows*nroRow + this.rows)
      let nros = row.map( celda => celda.nro).filter(e => e != 0)
      let s = new Set(nros)
      
      if( nros.length > s.size ) {
        this.filasRojas.push(nroRow)
      } else {
        let index = this.filasRojas.indexOf(nroRow)
        if (index >= 0) {
          this.filasRojas.splice(index,1)
        }
      }
    },
    checkColumn(nroCelda){

      let nroCol = nroCelda%this.rows
      
      let column = this.puzzle.filter( (celda, index) => index%this.rows === nroCol )
      let nros = column.map( celda => celda.nro ).filter(c => c != 0)
      let s = new Set(nros)
      
      if(nros.length > s.size ) {
        this.columnasRojas.push(nroCol)
      } else {
        let index = this.columnasRojas.indexOf(nroCol)
        if (index >= 0) {
          this.columnasRojas.splice(index,1)
        }
      }
    },
    checkSquare(nroCelda){
      
      let i = 0
      while(!this.squares[i].includes(nroCelda)){
        i++
      }
      let sqr = this.squares[i].map( value => this.puzzle[value].nro ) 
      let nros = sqr.filter(c => c != 0)
      let s = new Set(nros)

      if (nros.length > s.size) {
        this.cuadradosRojas.push(i)
        
      }else{
        let index = this.cuadradosRojas.indexOf(i)
        if (index >= 0) {
          this.cuadradosRojas.splice(index,1)
        }
      }
    },
    pintar(){

      this.puzzle.forEach(celda => celda.rojo = false)

      this.columnasRojas.forEach( col => {
        this.pintarColumn(col)
      })

      this.filasRojas.forEach( row => {
        this.pintarRow(row)
      })

      this.cuadradosRojas.forEach( sqr => {
        this.pintarSquare(sqr)
      })
    },
    pintarRow(nroRow){
      for( let i = nroRow*this.rows; i <  this.rows*nroRow + this.rows; i++ ) {
        this.puzzle[i].rojo = true
      }   
    },
    pintarColumn(nroCol){
      for( let i = nroCol; i <  this.rows*this.rows; i = i+9 ) {
          this.puzzle[i].rojo = true
        }
    },
    pintarSquare(nroSqr){
      this.squares[nroSqr].forEach( value => {
        this.puzzle[value].rojo = true
      })
    }
  }
}
</script>

<style>
body{
  margin: 0;
  padding: 0;
  background-color: #171A21;
  color: #AFB3F7

}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  
  
}

#main-c {
  display: grid;
  grid-template-columns: [first-column] 80vw [second-column] 20vw [last-c];
  grid-template-rows: [first-row] 20vh [second-row] 70vh [third-row] 10vh [last-r];

}

#header-c {

  grid-column: first-column / last-c;
  grid-row: first-row / first-row;

  text-align: center;
  
}

#header-c h1 {
  font-size: 5vh;
  margin-bottom: 0;

}

#header-c h3 {
  font-size: 2vh;
  margin-top: 0;
}

#right-menu-c {
  
  grid-column: second-column / second-column;
  grid-row: second-row / third-row;

  
  margin: 15vh 2vw 15vh 0;
  background-color: #92BCEA;
  border-radius: 25px;
  box-shadow: 5px 5px 10px #7a7cac;
}

#buttons-c {
  margin: 15vh 2vw 15vh 2vw;
  display: flex;
  flex-direction: column;
}

#board-c {  
  grid-column: first-column / first-column;
  grid-row: second-row / second-row;

  margin: 2vw;
  background-color: #617073;
  border-radius: 25px;
  box-shadow: 5px 5px 10px #7a7cac;
}

#footer{
  grid-column: first-column / last-c;
  grid-row: third-row / third-row;

  text-align: center;
  margin-top: 5vh;
  font-size: 2vh;
}

.grid-item {
  
   
}
</style>
