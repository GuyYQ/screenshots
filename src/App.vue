<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link>
    </div>
    <router-view/>
  </div>
</template>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}

#myCanvas {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 9998;
    overflow: hidden;
}

.retc {
    position: absolute;
    border: 1px solid #CCCCCC;
    background-color: rgba(49, 108, 236, 0.1);
    overflow: hidden;
    z-index: 99999;
}

.div {
    position: absolute;
    border: 1px dashed red;
    width: 0px;
    height: 0px;
    left: 0px;
    top: 0px;
    overflow: hidden;
    z-index: 99999;
}

.dialog-retc {
    position: absolute;
    background-color: rgba(0, 0, 0, 0.5);
    width: 0px;
    height: 0px;
    left: 0px;
    top: 0px;
    overflow: hidden;
    z-index: 9999;
}

#myCanvas {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 9998;
    overflow: hidden;
}

.retc-done {
    width: 100px;
    height: 100px;
}
</style>
