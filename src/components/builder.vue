<template>
<div class="noselect">
    <div id="svgTest"> </div>
    <!-- <div id="selection" v-if="target" v-bind:style="selectionShape"></div> -->
    <div
        v-on:mousemove="mousemove" 
        v-on:mousedown="mousedown" 
        v-on:mouseup="mouseup"
    >
    
    <div id="container" class="fixed" style="background:#a4a4a4" >
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
        this.circleSize = 15;
        this.targetOffsetPosition = {x:0,y:0};
        this.target = undefined;
        this.lastMovePosition = { x: 0 , y:0};
        this.resize = {
            enable : false,
            target : "none",
            lastWidth : 0
        }
    }

    init () {
        const t = this;
        this.draw = SVG('svgTest')
        this.rect = this.draw.rect();

        this.makers.left = this.draw.circle(this.circleSize).fill('#f06');
        this.makers.left.id("makers-left");
        this.makers.left.on("mousemove", function(event) {
            if (t.resize.enable == false)
                return;
            
            let {x ,y } = event;
            
            var  offsetW =  t.lastMovePosition.x - x;
            var width = t.target.clientWidth + offsetW;
            t.target.style.width =  t.target.clientWidth + offsetW + "px";
            console.log("changeWidth", x, y);
            
            t.target.style.left = t.target.offsetLeft - offsetW + "px";
            t.lastMovePosition.x = x;
            t.lastMovePosition.y = y;
            
            t.drawRectange(x,y,width,t.target.height);
            this.move(x - 7.5,y - 7.5);
        });

        this.makers.left.on("mousedown", function(event) {
            console.log(this,"maker-left");
            let {x ,y } = event;
             t.resize.enable = true;
             t.resize.target = "left";
             t.targetOffsetPosition.x = x ;
             t.targetOffsetPosition.y = y;
        });

        this.makers.left.on("mouseup", function() {
            t.resize.enable = false;
        })

        

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
        console.log("moveElement")
        // if (this.resize.enable) {
            
        //     target.style.left = x + "px";

        //     //target.style.width = this.style.width
        //     //target.style.width = this.style.width
        //     var offsetW =  this.lastMovePosition.x - x;
        //     target.style.width =  target.clientWidth + offsetW + "px";
        //     //this.lastMovePosition.y - y;

        //     this.lastMovePosition.x = mouse_x;
        //     this.lastMovePosition.y = mouse_y;
        //     console.log(x,y);
        // } else 
        {
            
            this.drawRectange(target.offsetLeft,target.offsetTop, target.offsetWidth, target.offsetHeight);
            target.style.position = "absolute";
            target.style.left = x - this.targetOffsetPosition.x + "px";
            target.style.top = y - this.targetOffsetPosition.y+ "px";
        }
    }



    mouseDown(target, target_x, target_y, mouse_x, mouse_y) {
        this.resize.enable = false;
        this.target = target;
        
        this.targetOffsetPosition.x = mouse_x - target_x;
        this.targetOffsetPosition.y = mouse_y - target_y;
        this.lastMovePosition.x = mouse_x;
        this.lastMovePosition.y = mouse_y;
        //console.log(target_x,target_y,mouse_x, mouse_y);
    }

    mouseUp() {
        // this.tar
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
                this.selection.mouseDown(this.target, this.target.offsetLeft, this.target.offsetTop, this.mouse.position.x , this.mouse.position.y);
                this.selection.drawRectange(this.target.offsetLeft,this.target.offsetTop, this.target.offsetWidth, this.target.offsetHeight);                
            }
        },
        mouseup(event) {
            this.mouse.pressed = false;
            this.selection.mouseUp();
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

    .noselect {
  -webkit-touch-callout: none; /* iOS Safari */
    -webkit-user-select: none; /* Safari */
     -khtml-user-select: none; /* Konqueror HTML */
       -moz-user-select: none; /* Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
            user-select: none; /* Non-prefixed version, currently
                                  supported by Chrome and Opera */
}

</style>
<style>

    #makers-left {
        color: red;
        pointer-events: all;
    }
    
</style>
