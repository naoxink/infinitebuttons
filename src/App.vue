<template>
  <div id="app">

    <ul class="mainMenu">
      <li @click="save">Guardar</li>
      <li @click="load">Cargar</li>
    </ul>

    <p class="available">Disponible: {{ available }}</p>
    <button id="buyButton" :disabled="canBuyNewButton()" @click="buyNewButton">Nuevo botón - Coste: {{ newButtonCost }}</button>
    <Button v-for="n in buttons" :key="n" :number="n" :available="available" v-on:incrementAvailable="incrementAvailable"></Button>
    <input type="hidden" id="shareText">

  </div>
</template>

<script>
import Button from './components/Button.vue'

export default {
  name: 'app',
  components: {
    Button
  },
  data: () => {
    return {
      'available': 0,
      'buttons': 1,
      'newButtonCost': 0.05
    }
  },
  methods: {
    incrementAvailable(qty){
      this.available += parseFloat(qty, 10)
    },
    buyNewButton(){
      this.buttons++
      this.available -= this.newButtonCost
      this.newButtonCost *= this.buttons * 1.5
    },
    canBuyNewButton(){
      return this.newButtonCost >= this.available
    },
    getButtonsLevel(){
      return [...document.querySelectorAll('.workerButton')].map(b => b.getAttribute('level')).join(', ')
    },
    copyToShare(){
      let shareText = document.querySelector('#shareText')
      shareText.setAttribute('type', 'text')
      shareText.select()

      try {
        var successful = document.execCommand('copy');
        var msg = successful ? 'successful' : 'unsuccessful';
        alert('¡Copiado al portapapeles!');
      } catch (err) {
        alert('Jo, no se ha podido copiar al portapapeles :(');
      }

      shareText.setAttribute('type', 'hidden')
      window.getSelection().removeAllRanges()
    },
    share(){
      let txt = `
  Tengo disponible: ${this.available}.
  Tengo ${this.buttons} botones.
  Los niveles de mis botones son: ${this.getButtonsLevel()}
      `
      document.querySelector('#shareText').value = txt
      this.copyToShare()
    },
    save(){
      const buttonsData = this.$children.map(b => {
        return {
          level: b.level,
          cost: b.cost,
          increment: b.increment,
          maxed: b.maxed,
          maxLevel: b.maxLevel
        }
      })
      const data = {
        'available': this.available,
        'newButtonCost': this.newButtonCost,
        'buttons': this.buttons,
        'buttonsData': buttonsData
      }
      localStorage.setItem('infiniteincremental-save', JSON.stringify(data))
    },
    load(){
      let data = localStorage.getItem('infiniteincremental-save')
      if(!data) return
      data = JSON.parse(data)
      this.available = data.available
      this.buttons = data.buttons
      this.newButtonCost = data.newButtonCost
      data.buttonsData.forEach((buttonData, index) => {
        this.$children[index].level = buttonData.level
        this.$children[index].cost = buttonData.cost
        this.$children[index].increment = buttonData.increment
        this.$children[index].maxed = buttonData.maxed
        this.$children[index].maxLevel = buttonData.maxLevel
      })
    }
  },
  mounted(){
    this.load()
  }
}
</script>

<style>
#app {
  font-family: monospace;
  text-align: center;
  color: #333;
  margin-top: 15px;
}
#buyButton:disabled {
  opacity: .5;
}
#buyButton {
  margin: 20px;
  padding: 10px 20px;
  background-color: lightblue;
  border: 1px solid grey;
  color: navy;
  cursor: pointer;
}
div.warning {
  color: red;
  padding: 10px;
  border: 1px solid red;
  margin: 20px;
  display: inline-block;
}
ul.mainMenu {
  list-style-type: none;
  margin: 0;
  padding: 0;
}
ul.mainMenu > li {
  display: inline-block;
  cursor: pointer;
  padding: 5px 10px;
  margin: 5px;
  color: grey;
  border: 1px solid grey;
}
ul.mainMenu > li:hover {
  background-color: lightgrey;
  color: #333;
}
ul.mainMenu > li:active {
  background-color: #333;
  color: lightgrey;
}
.available {
  margin-top: 30px;
  font-size: 1.5em;
}
</style>
