<template>
  <div id="app">
    <ScreenCapture class="capture" @captured="present"></ScreenCapture>
    <figure v-if="src" class="presentation">
      <div>截图结果</div>
      <img :src="src">
    </figure>
  </div>
</template>

<script>
import ScreenCapture from "@/components/ScreenCapture.vue";

export default {
  name: "App",
  components: {
    ScreenCapture
  },
  data() {
    return {
      src: undefined
    };
  },
  methods: {
    present ({ error, imageData }) {
      if (error) {
        console.error('Capture error', error);
        return;
      }
      this.src = imageData;
    }
  }
};
</script>

<style>
* {
  box-sizing: border-box;
}

html, body, #app {
  height: 100%;
  margin: 0;
}

#app {
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 100%;
  padding: 2em;
}

.capture {
  height: 40px;
  margin: 0;
}

.presentation {
  flex: 1;
  width: 100%;
  text-align: center;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
  overflow: auto;
}

.presentation img {
  margin-top: 20px;
  border: 1px solid green;
}
</style>
