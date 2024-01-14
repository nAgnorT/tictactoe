<template>

  <div class="tic-tac-toe">
    <ticTacToe />
  </div>
</template>

<script>
import ticTacToe from './components/ticTacToe.vue'

export default {
  name: 'App',
  components: {
    ticTacToe
  }
}
</script>