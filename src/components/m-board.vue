<template>
  <div class="board">
    <div class="frame-wrapper" :style="frameSize">
      <p v-if="valid" class="win">Congratulations!</p>
      <div class="frame" :style="frameSize">
        <m-tile v-for="tile in tiles"  
          @moveTile="moveTile"  
          :tile="tile" 
          :key="tile.position"
        />
      </div>
    </div>
    <div class="controls">
      <a class="restart" href="#" @click.prevent="restart">New Game</a>
    </div>    
  </div>
</template>

<script>
import sample from "lodash/sample";
import MTile from "@/components/m-tile";
export default {
  data() {
    return {
      image: null,
      showingOriginal: false,
      size: {
        horizontal: 0,
        vertical: 0
      },
      tiles: [],
      tileSize: {
        width: 0,
        height: 0
      }
    };
  },
  components: {
    MTile
  },
  methods: {
    gameStart({ image, size }) {
      this.image = image;
      this.size = size;
      const img = new Image();
      img.src = image;
      img.onload = () => {
        this.tileSize.width = Math.floor(img.width / size.horizontal);
        this.tileSize.height = Math.floor(img.height / size.vertical);
        this.generateTiles();
        this.shuffleTiles();
      };
    },
    generateTiles() {
      const { horizontal } = this.size;
      const { width: w, height: h } = this.tileSize;
      this.tiles = [];
      for (let i = 0; i < this.totalTiles; ++i) {
        const isEmpty = i === 0;
        const position = i;
        const background = i === 0 ? "transparent" : `url(${this.image})`;
        const backgroundPositionX = `-${(i % horizontal) * w}px`;
        const backgroundPositionY = `-${Math.floor(i / horizontal) * h}px`;
        const width = `${w}px`;
        const height = `${h}px`;
        const order = i;

        const obj = {
          styles: {
            background,
            backgroundPositionX,
            backgroundPositionY,
            width,
            height,
            order
          },
          position,
          isEmpty
        };

        this.tiles.push(obj);
      }
    },
    shuffleTiles() {
      for (let i = 0, j = this.totalTiles * 3; i < j; ++i) {
        const emptyTile = this.tiles.find(t => t.isEmpty);
        const movableTiles = this.tiles.filter(t => {
          return this.getMovableOrders(emptyTile).includes(t.styles.order);
        });
        this.switchTiles(emptyTile, sample(movableTiles));
        // 隨機取一個movableTiles跟emptyTile換位置
      }
    },
    moveTile(tile) {
      if (tile.isEmpty) return;

      const target = this.tiles.find(t => {
        const isMovable = this.getMovableOrders(tile).includes(t.styles.order);
        return t.isEmpty && isMovable;
      });

      target && this.switchTiles(target, tile);
    },
    switchTiles(a, b) {
      [a.styles.order, b.styles.order] = [b.styles.order, a.styles.order];
    },
    getMovableOrders(tile) {
      const pos = tile.styles.order;
      const { horizontal: h } = this.size;
      const arr = [
        pos % h ? pos - 1 : null, //left
        (pos + 1) % h ? pos + 1 : null, //right
        pos - h, //up
        pos + h //down
      ];
      //已空tile來看, 可以移動的tile位置編號
      return arr;
    },
    restart() {
      this.$emit("restart");
    }
  },
  computed: {
    frameSize() {
      const { width, height } = this.tileSize;
      const { horizontal, vertical } = this.size;

      return {
        width: `${width * horizontal}px`,
        height: `${height * vertical}px`
      };
    },
    totalTiles() {
      return this.size.horizontal * this.size.vertical;
    },
    valid() {
      if (!this.tiles.length) return false;

      const notMatch = this.tiles.some(
        item => item.styles.order !== item.position
      );
      if (notMatch) return false;
      // for (let i = 0; i < this.totalTiles; ++i) {
      //   if (this.tiles[i].styles.order !== this.tiles[i].position) {
      //     return false
      //   }
      // }
      return true;
    }
  }
};
</script>

<style lang="scss">
.frame-wrapper {
  margin: 0 auto;
  position: relative;
  box-shadow: 0 0 0px 10px;
  .original {
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    width: 100%;
  }
  p.win {
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba(0, 0, 0, 0.5);
    color: #fff;
    font-size: 32px;
    margin: 0 0;
    background: rgba(43, 181, 82, 0.7);
    text-transform: uppercase;
  }
}
.frame {
  display: flex;
  flex-wrap: wrap;
  background-color: gray;
  background-size: cover;
}
.controls {
  margin-top: 30px;
  a {
    display: inline-block;
    text-decoration: none;
    padding: 6px 12px;
    background: #f78403;
    color: #fff;
    border-radius: 3px;
    &.toggle-original {
      background: #d05b88;
    }
    &.restart {
      background: #368ba0;
    }
    &.shuffle {
      background: #3ebb5c;
    }
  }
}
</style>
 