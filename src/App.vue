<template>
  <div id="app">
    <mile-stone :items="items"></mile-stone>
    <div class="the-vue">
      <img src="./assets/logo.png">
    </div>
  </div>
</template>

<script>
import MileStone from './components/MileStone'

export default {
  name: 'app',
  components: {
    MileStone
  },

  data () {
    return {
      items: [
        { name: 'A', dateStart: '2017-01-29', dateEnd: '2017-02-01' },
        { name: 'B', dateStart: '2017-02-02', dateEnd: '2017-02-04' },
        { name: 'C', dateStart: '2017-01-28', dateEnd: '2017-01-29' },
        { name: 'D', dateStart: '2017-01-29', dateEnd: '2017-01-31' },
        { name: 'E', dateStart: '2017-02-01', dateEnd: '2017-02-02' },
        { name: 'F', dateStart: '2017-01-29', dateEnd: '2017-01-30' },
        { name: 'G', dateStart: '2017-01-28', dateEnd: '2017-01-30' }
      ]
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #676767;
}

.the-vue {
  text-align: center;
}
</style>
