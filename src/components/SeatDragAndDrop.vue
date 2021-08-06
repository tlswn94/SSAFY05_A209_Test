<template>
    <div>
        <div  style="margin-top:10px;" >
            <grid-layout :layout.sync="layout"
                         :col-num="20"
                         :row-height="30"
                         :is-draggable="false"
                         :is-resizable="false"
                         :vertical-compact="false"
                         :use-css-transforms="true"
            >
               <seatModalUser v-if="seatModalVisible" ref="seatModal" :seatData ="seatModalData" :index="dataidx" :show="seatModalVisible" @close="closeSeatModal" @closeReserve="showNoticeModal" ></seatModalUser>
               <noticeRuleModal v-if="ruleModalVisible" @closeNotice="closeNoticeModal" @closeWithAccept="showReserveModal"></noticeRuleModal>
               <reserveModal v-if="reserveModalVisible" @closeReserve="closeReserveModal"></reserveModal>
               <grid-item v-for="(item,idx) in this.layout"
                           :key = "idx"
                           :x="item.x"
                           :y="item.y"
                           :w="item.w"
                           :h="item.h"
                           :i="item.i"

                :class="seatMyClass(idx)">
                <div class ="seatClickArea" @click="showSeatModal(item,idx)">

                    <span class="text" v-if="item.i != '출입문'">{{item.i}}</span>


                </div>

                </grid-item>

            </grid-layout>

          <div>
</div>
        </div>
    </div>
</template>

<script>
import { GridLayout, GridItem } from "vue-grid-layout"
import seatModalUser from "../components/Modal/SeatModalUser.vue"
import noticeRuleModal from "../components/Modal/NoticeRuleModal.vue"
import reserveModal from "../components/Modal/ReservationModal.vue"

export default {
    name : "moving",
    components: {
        GridLayout,
        GridItem,
        seatModalUser,
        noticeRuleModal,
        reserveModal,
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
            index: 6,
            colNum : 20,
            draggable : true,
            seatModalData :  {x:16, y :0,  w :4, h :4, i  :  "출입문" ,  used :false},
            seatModalVisible : false,
            ruleModalVisible : false,
            reserveModalVisible : false,
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
        closeReserveModal(){

          this.reserveModalVisible = false;
        },
        showReserveModal(){

          this.reserveModalVisible = true;
          console.log("동의했습니다~")
        },

        closeNoticeModal(){

          this.ruleModalVisible = false
        },

        showNoticeModal(){

          this.seatModalVisible = false;
          this.ruleModalVisible = true;


        },
        useNow(idx){

          this.layout[idx].used = true
          this.layout[idx].id = "TempUser"
          this.$store.commit('seat/updateSeat',this.layout)


        },
        closeSeatModal(){

          this.seatModalVisible = false;


        },
        showSeatModal(item,idx){

          if(item.i != "출입문")
          {

             this.seatModalData = item
             this.dataidx = idx
          this.seatModalVisible = true;
          }


        },
        seatMyClass(idx){
          if(this.layout[idx].used==true)
          {
            return "seatOccupied"
          }
          else if(this.layout[idx].i == "출입문")
          {
            return "seatExit"
          }
          else{

            return "seat"
          }


      },
      onDrag(e) {
            e.target.style.transform = e.transform;
        },
        toggleDraggable() {
            this.draggable = !this.draggable;
        }
    }

}
</script>

<style scoped>
.vue-grid-layout {
    background: white;
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
.seat{
    background-image : url('../assets/seat.png');
    background-size : 100% 100%;
    background-repeat: no-repeat;


}

.seatOccupied{
    background-image : url('../assets/usedSeat.png');
     background-size : 100% 100%;
    background-repeat: no-repeat;


}

.seatExit{

  background-image : url('../assets/exit.png');
    background-size : 100% 100%;
    background-repeat: no-repeat;

}

.seatClickArea{

  height : 100%;
  width : 100%;
  cursor: pointer;
}

.seatReserve-Btn{



}

</style>
