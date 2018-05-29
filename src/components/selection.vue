<template>
<div v-on:mousemove="mousemove" 
     v-on:mousedown="mousedown" 
     v-on:mouseup="mouseup"
     v-on:click="onclick"
     >
    <div id="container" >
    <h1> hello</h1>    
    </div>
    <p>{{ mouse }}  </p>
    <div id="selection" v-if="pressed" v-bind:style="mouse.selection"></div>
</div>
</template>

<script>

export default {
  name: 'builder',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      mouse : { 
          position: {},
          selection : { x: 0, y:0, width:0, height:0},
          pressed : false
        },
    }
  },
  methods :{
      mousemove(event ) {
        this.mouse.position = { x: event.offsetX , y: event.offsetY };
        this.mouse.selection.width = event.offsetX;
        this.mouse.selection.height = event.offsetY;

      },
      mousedown(event) {
        this.mouse.pressed = true;  
        this.mouse.selection.x = event.offsetX;
        this.mouse.selection.y = event.offsetY;
      },
      mouseup(event) {
          this.mouse.pressed = false;
      },
      onclick(){
      }

  }, 
}
</script>
<style scoped>
    #container {
        width:  500px;
        height : 500px;
        border:1px solid black;
    }
</style>