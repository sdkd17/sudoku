<template>
  <div id="tablero-container"> 
    <div id="tablero" class="grid-item"> 
    <div 
      v-for="i in celdas.length" 
      :key="i" 
      :class="classObject[i-1]"
      @click.left.prevent="$emit('leftClick', i-1)"
      @click.right.prevent="$emit('rightClick',i-1)"

    > 
      <span v-show="celdas[i-1].nro != 0"> 
        {{ celdas[i-1].nro }}
      </span>
      
    </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    celdas: Array,
  },
  data(){
    return{
     
    }
  },
  watch: {

  },
  computed: {
    classObject(){
      
      let arrayClasses = []
      
      for (let i=0; i< this.celdas.length; i++) {
        let classes = {
          'celda': true, 
          'right-border': false,
          'bottom-border': false, 
          'celda-fija': false, 
          'rojo': this.celdas[i].rojo, 
          'verde':  this.celdas[i].verde
        }
        if (i%9 === 2 || i%9 === 5 ) { 
          classes['right-border'] = true
        }

        if ( (i >= 18 && i < 27) || (i >= 45 && i < 54) ) {
          classes['bottom-border'] = true
        }

        if (!this.celdas[i].clickable){
          classes['celda-fija'] = true
        }
        arrayClasses.push(classes)  
      }
      return arrayClasses
    }
  }
}
</script>

<style>
#tablero-container {
  display: grid;
  grid-template-columns: 15vw 50vw 15vw;
  grid-template-rows: 10vh 50vh 10vh;
}

#tablero {
  grid-column: 2/ span 1;
  grid-row: 2/ span 1;

  display: grid;
  grid-template-columns: repeat(9, 5.455vw);
  grid-template-rows: repeat(9, 5.420vh);

  border: solid 0.5vw #171A21
}

.celda {
  border: solid 1px #617073;
  font-size: 4vh;
  text-align: center;
  background-color: antiquewhite;
  color: #171A21

}

.right-border {
  border-right: solid 3px red;
}

.bottom-border {
  border-bottom: solid 3px red;
}

.celda-fija{
  background-color: #AFB3F7;
}

.rojo {
  color: red
}
</style>