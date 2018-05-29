<template>
<div >
    <div
        v-on:mousemove="mousemove" 
        v-on:mousedown="mousedown" 
        v-on:mouseup="mouseup"
    >

    <div id="container" >
        <h1>hello</h1>
        <span>id</span>
        <div><span>df</span></div>
    </div>
    <div id="selection" v-if="mouse.pressed" v-bind:style="selectionShape"></div>
    <div id="rightpanel">
        <p>{{ mouse }}  {{ selectionShape }}  </p>
    </div>
</div>
</div>
</template>

<script>

export default {
    name: 'builder',
    data () {
        return {
            container :{},
            mouse : { 
                position: {},
                selection : { x: 0, y:0, right:0, bottom:0},
                pressed : false
                },
            selectionShape :{
                left: 0 + "px",
                top: 0 + "px",
                width: 0 + "px",
                height: 0 + "px",
            }
        }
    },
    methods :{
        mousemove(event ) {
            if (!this.mouse.pressed)
                return;
            this.mouse.position = { x: event.x , y: event.y };
            this.mouse.selection.right = event.x;
            this.mouse.selection.bottom = event.y;
            this.selectionShape.left = this.mouse.selection.x + "px";
            this.selectionShape.top = this.mouse.selection.y + "px";
            this.selectionShape.width = this.mouse.selection.right - this.mouse.selection.x + "px";
            this.selectionShape.height = this.mouse.selection.bottom -this.mouse.selection.y+ "px";
        },
        mousedown(event) {
            this.mouse.pressed = true;  
            this.mouse.selection.x = event.x;
            this.mouse.selection.y = event.y;
        },
        mouseup(event) {
            this.mouse.pressed = false;
            this.selectionShape.left = "0px";
            this.selectionShape.top = "0px";
            this.selectionShape.width = "0px";
            this.selectionShape.height = "0px";
        }
    }, 
    mounted :function(){
        console.log(arguments);
        this.container = this.$el.querySelector("#container")
        console.dir(this.container.children[0]);
    }
}
</script>
<style scoped>
    #container {
        height : 500px;
        border:1px solid black;
    }
    #selection {
        border:1px solid red;
        position: absolute;
    }
    #rightpanel {
        position: absolute;
        left : 100% - 50px;
        width: 300px;
        height: 500px;;
    }
</style>