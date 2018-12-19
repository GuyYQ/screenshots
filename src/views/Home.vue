<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <ScreenShots @modalConfirm="confirm" />
  </div>
</template>

<script>
// @ is an alias to /src
import ScreenShots from '@/components/screen-shots.vue'

export default {
  name: 'home',
  components: {
    ScreenShots
  },
  methods: {
         // 是否发送截图
        confirm(src,blob){
            let img = document.createElement("img");
            img.src = src;
            document.body.appendChild(img);
        },
    },
}

</script>
