<template>
    <div>
        <div  style="margin-top:10px;" >
            <button type="button" class="btn btn-info mb-3" @click="seatAddItem()">좌석 추가</button>
            <button type="button" class="btn btn-info mb-3" @click="seatRemoveItem()">좌석 삭제</button>
            <grid-layout :layout.sync="layout"
                         :col-num="colNum"
                         :row-height="30"
                         :is-draggable="true"
                         :is-resizable="true"
                         :vertical-compact="false"
                         :use-css-transforms="true"
            >
               <seatModalAdmin v-if="modalVisible" ref="Modal" :seatData ="modalData" :index="dataidx" :show="modalVisible" @close="closeModal" @updateOccupy="seatChangeState" ></seatModalAdmin>

               <grid-item v-for="(item,idx) in layout"
                           :key = "idx"
                           :x="item.x"
                           :y="item.y"
                           :w="item.w"
                           :h="item.h"
                           :i="item.i"

                :class="seatMyClass(idx)"

                >
                <div class ="seatClickArea">
                      <span class="text" @click.right.prevent="seatShowModal(item,idx)">{{item.i}}</span>


                    </div>

                </grid-item>
            </grid-layout>
        </div>
        <button type="button" class="btn btn-info mt-3 seatSave-Btn" @click="seatUpdateAll()">저장</button>
    </div>
</template>

<script>
import { GridLayout, GridItem } from "vue-grid-layout"
import seatModalAdmin from "./Modal/SeatModalAdmin.vue"
export default {
    name : "movingadmin",
    components: {
        GridLayout,
        GridItem,
        seatModalAdmin
    },
    mounted(){

         this.layout = JSON.parse(JSON.stringify(this.$store.getters['seat/getSeatData']))
         this.colNum = JSON.parse(JSON.stringify(this.$store.getters['seat/getColNum']))
         this.index = JSON.parse(JSON.stringify(this.$store.getters['seat/getIndex']))



    },
    data() {

        return {
            layout: [
            ],
            draggable: true,
            resizable: true,
            colNum : 0,
            index: 0,
            eventLog: [],
            modalData :{x:16, y :0,  w :4, h :4, i  :  "출입문" ,  used :false},
            modalVisible : false,
            dataidx : 0
        }
    },
    watch: {
        eventLog: function() {
            const eventsDiv = this.$refs.eventsDiv;
            eventsDiv.scrollTop = eventsDiv.scrollHeight;
        }
    },
    methods: {
       closeModal(){

          this.modalVisible = false;


        },
        seatChangeState(e){
          this.layout[e].used = false
          this.layout[e].id = null

        },
        seatShowModal(item,idx){

          if(item.i != "출입문")
          {

             this.modalData = item
             this.dataidx=idx
          this.modalVisible = true;
          }


        },
         seatUpdateAll(){


          this.$store.commit('seat/updateSeat',this.layout)
          this.$store.commit('seat/updateIndex',this.index)
          this.$store.commit('seat/updateColnum',this.colNum)
         },
         seatAddItem() {
            // Add a new item. It must have a unique key!
            this.layout.push({
                x: (this.layout.length * 2) % (this.colNum || 20),
                y: this.colNum/2, // puts it at the bottom
                w: 2,
                h: 2,
                i: this.index,
                used : false,
            });
            // Increment the counter to ensure key is always unique.
            this.index++;
        },
        seatRemoveItem() {
            if(this.index >1)
            {

              this.layout.splice(-1, 1);
              this.index--
            }


        },
        moveEvent: function(i, newX, newY){
            const msg = "MOVE i=" + i + ", X=" + newX + ", Y=" + newY;
            this.eventLog.push(msg);
            console.log(msg);
        },
        movedEvent: function(i, newX, newY){
            const msg = "MOVED i=" + i + ", X=" + newX + ", Y=" + newY;
            this.eventLog.push(msg);
            console.log(msg);
        },
        resizeEvent: function(i, newH, newW, newHPx, newWPx){
            const msg = "RESIZE i=" + i + ", H=" + newH + ", W=" + newW + ", H(px)=" + newHPx + ", W(px)=" + newWPx;
            this.eventLog.push(msg);
            console.log(msg);
        },
        resizedEvent: function(i, newX, newY, newHPx, newWPx){
            const msg = "RESIZED i=" + i + ", X=" + newX + ", Y=" + newY + ", H(px)=" + newHPx + ", W(px)=" + newWPx;
            this.eventLog.push(msg);
            console.log(msg);
        },
        containerResizedEvent: function(i, newH, newW, newHPx, newWPx){
            const msg = "CONTAINER RESIZED i=" + i + ", H=" + newH + ", W=" + newW + ", H(px)=" + newHPx + ", W(px)=" + newWPx;
            this.eventLog.push(msg);
            console.log(msg);
        },
        layoutCreatedEvent: function(newLayout){
            this.eventLog.push("Created layout");
            console.log("Created layout: ", newLayout)
        },
        layoutBeforeMountEvent: function(newLayout){
            this.eventLog.push("beforeMount layout");
            console.log("beforeMount layout: ", newLayout)
        },
        layoutMountedEvent: function(newLayout){
            this.eventLog.push("Mounted layout");
            console.log("Mounted layout: ", newLayout)
        },
        layoutReadyEvent: function(newLayout){
            this.eventLog.push("Ready layout");
            console.log("Ready layout: ", newLayout)
        },
        layoutUpdatedEvent: function(newLayout){
            this.eventLog.push("Updated layout");
            console.log("Updated layout: ", newLayout)
        },
        seatMyClass : function(idx){
          if(this.layout[idx].used)
          {
            return "seatOccupied"
          }
          else{

            return "seat"
          }


      }


    }

}
</script>

<style scoped>
.vue-grid-layout {
    background: #eee;
}

.vue-grid-item .resizing {
    opacity: 0.9;
}
.vue-grid-item .static {
    background: #cce;
}
.vue-grid-item .text {
    font-size: 24px;
    text-align: center;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
    height: 100%;
    width: 100%;
}
.vue-grid-item .no-drag {
    height: 100%;
    width: 100%;
}
.vue-grid-item .minMax {
    font-size: 12px;
}
.vue-grid-item .add {
    cursor: pointer;
}
.vue-draggable-handle {
    position: absolute;
    width: 20px;
    height: 20px;
    top: 0;
    left: 0;
    background: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='10' height='10'><circle cx='5' cy='5' r='5' fill='#999999'/></svg>") no-repeat;
    background-position: bottom right;
    padding: 0 8px 8px 0;
    background-repeat: no-repeat;
    background-origin: content-box;
    box-sizing: border-box;
    cursor: pointer;
}
.layoutJSON {
    background: #ddd;
    border: 1px solid black;
    margin-top: 10px;
    padding: 10px;
}
.eventsJSON {
    background: #ddd;
    border: 1px solid black;
    margin-top: 10px;
    padding: 10px;
    height: 100px;
    overflow-y: scroll;
}
.remove {
    position: absolute;
    right: 2px;
    top: 0;
    cursor: pointer;
}
.seat{
   background:skyblue;
    border: 1px solid black;

}

.seatOccupied{
   background:rgb(235, 135, 135);
    border: 1px solid black;

}

.seatSave-Btn{

  left : 92%;


}

.seatClickArea{




}

</style>
