<template>
<div >
    <div id="svgTest"> </div>
    <!-- <div id="selection" v-if="target" v-bind:style="selectionShape"></div> -->
    <div
        v-on:mousemove="mousemove" 
        v-on:mousedown="mousedown" 
        v-on:mouseup="mouseup"
    >
    
    <div id="container" class="fixed" >
        <h1>hello</h1>
        <span>id</span>
        <button>collection</button>
        <div><span>df</span></div>
    </div>
    
    <div id="rightpanel">
        <p>{{ mouse }}  {{ selectionShape }} {{ targetBounds}}            
        </p>
        <div >target : {{ target ? target.nodeName : 'none' }} </div>
    </div>
</div>
</div>
</template>

<script>

class SelectionBox{

    constructor() {
        this.draw = undefined;
        this.rect = undefined;
        this.makers = {};
        this.circleSize = 5;
    }

    init () {

        this.draw = SVG('svgTest')
        this.rect = this.draw.rect();

        this.makers.left = this.draw.circle(this.circleSize).fill('#f06');
        // this.makers.left.id("makers-left");
        // this.makers.left.on("mousemove", function() {
        //     console.log("move left");
        // });

        // this.makers.left.on("click", function() {
        //     console.log("click");
        // });
        this.makers.right = this.draw.circle(this.circleSize).fill('#f06');
        this.makers.top = this.draw.circle(this.circleSize).fill('#f06');
        this.makers.bottom = this.draw.circle(this.circleSize).fill('#f06');


        this.rect.fill('transparent').stroke({ width: 0.5,color:'#f06' })

    }

    drawRectange(x,y,width,height) {
        this.rect.move(x,y);
        this.rect.width(width);
        this.rect.height(height);

        this.makers.left.move(x - 5/2,y + height /2 - 5/2);
        this.makers.right.move(x + width- 5/2, y + height/2 - 5/2);
        this.makers.top.move(x + width / 2- 5/2 , y- 5/2);
        this.makers.bottom.move(x + width / 2- 5/2, y + height- 5/2);
    }

    moveElement(target, x,y) {
        
        this.drawRectange(target.offsetLeft,target.offsetTop, target.offsetWidth, target.offsetHeight);
        target.style.position = "absolute";
        target.style.left = x + "px";
        target.style.top = y + "px";

    }
}

export default {
    name: 'builder',
    data () {
        return {
            container :{},
            target : undefined,
            selection : new SelectionBox(),
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
    computed : {
        targetBounds(){
            var t=this.target;
            if (this.target) {
                return {
                    offsetLeft:t.offsetLeft,
                    offsetTop:t.offsetTop,
                    offsetWidth:t.offsetWidth,
                    offsetHeight:t.offsetHeight
                    
                }
            }
            else {
                return undefined;
            }
            

            // return { this.target.offsetLeft,
            // {{ target.offsetTop }}
            // {{ target.offsetWidth }}
            // {{ target.offsetHeight }}
        }
    },
    methods :{
        mousemove(event ) {
            if (this.mouse.pressed && this.target) {
                //console.dir();
                if (this.target.classList.contains("fixed"))
                    return;
                this.selection.moveElement(this.target, event.x, event.y);
            }

        },
        mousedown(event) {

            this.mouse.position = { x: event.x , y: event.y };
            
            this.target = event.target;
            if (this.target) {
                this.mouse.pressed = true;
                this.selectionShape.left = this.target.offsetLeft + "px";
                this.selectionShape.top = this.target.offsetTop+ "px";
                this.selectionShape.width = this.target.offsetWidth+ "px";
                this.selectionShape.height = this.target.offsetHeight+ "px";
                
                this.selection.drawRectange(this.target.offsetLeft,this.target.offsetTop, this.target.offsetWidth, this.target.offsetHeight);                
            }
        },
        mouseup(event) {
            this.mouse.pressed = false;
        }
    }, 
    mounted :function(){
        this.selection.init();
    },

}
</script>
<style scoped>
    #container {
        height : 1200px;
        border:1px solid black;
    }
    #selection {
        border:1px solid red;
        position: absolute;
        pointer-events:none;
    }
    #rightpanel {
        position: fixed;
        left : 50px;
        top : 50px;
        background: white;
        border: 1px solid gray;
        border-radius: 5px;
        
        width: 200px;
        height: 500px;;
    }
    #svgTest {
        position: absolute;
        z-index: 1000;
        width: 100%;
        height: 100%;
        pointer-events:none;
    }

</style>
<style>

    #makers-left {
        color: red;
        pointer-events: all;
    }
    
</style>
