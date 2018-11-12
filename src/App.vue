<template>
  <div id="app">
    <header>
      <h1>Image Puzzle Game</h1>
    </header>
    <m-board ref="board" v-show="playing"  @restart="restart"  />
    <options-pane ref="optionsPane"  @gameStart="gameStart" v-show="!playing" />
  </div>
</template>

<script>
import MBoard from "@/components/m-board";
import OptionsPane from "@/components/options-pane";
export default {
  data() {
    return {
      playing: false
    };
  },
  components: {
    MBoard,
    OptionsPane
  },
  methods: {
    gameStart(data) {
      this.playing = true;
      this.$refs.board.gameStart(data);
    },
    restart() {
      this.playing = false;
      this.$refs.optionsPane.restart();
    }
  }
};
</script>

<style lang="scss" scoped>
#app {
  font: 14px/20px sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  header {
    h1 {
      font-weight: 100;
      height: 80px;
      line-height: 80px;
      font-size: 38px;
    }
  }
}
</style>
