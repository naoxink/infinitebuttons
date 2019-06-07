<template>
  <div class="buttonContainer">
    <button class="workerButton" :class="maxed ? 'maxed' : ''" :level="level" @click="upgrade" :disabled="canUpgrade()">{{ label }}</button>
    <div class="increment">Incremento: +{{ increment }}/s</div>
  </div>
</template>

<script>
export default {
  name: 'Button',
  props: {
    available: Number,
    number: Number
  },
  data: () => {
    return {
      level: 1,
      cost: 0.005,
      startTick: null,
      timer: null,
      increment: 0.001,
      maxed: false,
      maxLevel: 100
    }
  },
  computed: {
    label(){
      if(this.maxed){
        return `Nivel ${this.level} - Máximo`
      }else{
        return `Nivel: ${this.level} - Precio mejora: ${this.cost}`
      }
    }
  },
  methods: {
    canUpgrade(){
      return this.maxed || parseFloat(this.cost) >= parseFloat(this.available)
    },
    upgrade(){
      if(this.maxed) return
      this.level++
      this.$emit('incrementAvailable', this.cost * -1)
      if(this.level >= this.maxLevel){
        this.maxed = true
      }else{
        this.cost *= 1.3
        this.increment *= 1.2
      }
    },
    start(){
      this.startTick = Date.now()
      this.timer = setTimeout(() => {
        this.$emit('incrementAvailable', this.increment)
        this.start()
      }, 1000)
    }
  },
  mounted(){
    this.increment *= this.number
    this.cost *= this.number
    this.start()
  },
  beforeDestroy(){
    clearTimeout(this.timer)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
button:disabled {
  background-color: #f4f4f4;
  border-color: #AAA;
}
.buttonContainer {
  max-width: 1200px;
  margin: 0 auto;
}
button{
  outline: 0;
  border: 1px solid green;
  background-color: #D8FFB9;
  padding: 5px 10px;
  cursor: pointer;
  -webkit-transition: all .2s;
  -o-transition: all .2s;
  transition: all .2s;
  margin: 5px;
  width: 50%;
  display: inline-block;
}
button:not(:disabled):hover{
  background-color: #ACE480;
}
button:active{
  background-color: #f4f4f4;
  color: green;
}
button.maxed{ opacity: .5; color: navy; }
.increment {
  text-align: left;
  display: inline-block;
  width: 45%;
}
</style>
