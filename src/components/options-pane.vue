<template>
  <form id="optionsForm" @submit.prevent="start">
    <div class="img-wrapper">
      <img :src="image" v-if="image">
    </div>      
    <div class="content-wrapper">
      <div>
        <label for="file">
          <span>Select an image</span>          
          <input @change="fileChanged" type="file" id="file" accept="image/gif, image/jpeg, image/png">
        </label>
        <span v-if="image">
          <span>Board size:</span>        
          <input v-model.number="size.horizontal" type="number" name="width" min="2" max="10">
          <span>×</span>        
          <input v-model.number="size.vertical" type="number" name="height" min="2" max="10">
        </span>
      </div>
      <button v-if="image">Start</button>
    </div>
  </form>
</template>

<script>
import loadImage from "blueimp-load-image";
export default {
  data() {
    return {
      image: null,
      size: {
        horizontal: 3,
        vertical: 3
      }
    };
  },
  methods: {
    fileChanged(e) {
      if (!e.target.files.length) return (this.image = null);

      const selectedFile = e.target.files[0];
      const cb = canvas => (this.image = canvas.toDataURL());
      const config = {
        maxWidth: 600,
        maxHeight: 600,
        minWidth: 200,
        minHeight: 200,
        canvas: true
      };
      loadImage(selectedFile, cb, config);
    },
    start() {
      const { image, size } = this;
      const data = { image, size };
      this.$emit("gameStart", data);
    },
    restart() {
      this.image = null;
      document.querySelector("#optionsForm").reset();
    }
  }
};
</script>

<style lang="scss">
img {
  border: 1px solid #ccc;
  margin-bottom: 8px;
}
label[for="file"] {
  color: #368ba0;
  cursor: pointer;
  display: inline-block;
  margin-right: 12px;
}
input[type="number"] {
  height: 24px;
  font-size: 14px;
  text-align: center;
  border: 1px solid #ccc;
}
input[type="file"] {
  display: none;
}
button {
  -webkit-appearance: none;
  padding: 6px 12px;
  background: #1ca76a;
  color: #fff;
  border-radius: 3px;
  border: 0;
  font-size: 14px;
  cursor: pointer;
  margin-top: 10px;
}
</style>
 