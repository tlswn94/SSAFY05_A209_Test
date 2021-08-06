<template>
  <div class ="main">

    <base-header class="pb-6 pb-8 pt-5 pt-md-8 bg-gradient-success">
    </base-header>


    <!--Charts-->
    <b-container fluid class="mt--7">
      <b-row class="justify-content-center">
        <b-col lg="12">
          <card header-classes="bg-transparent">
            <h3 slot="header" class="mb-0">좌석 현황</h3>
              <!-- <b-table class="table-notice mt-5 mb-3" :items="items" :fields="fields" head-variant="light">
              </b-table> -->
              <div class= "board">
                <moving/>
              </div>
          </card>
        </b-col>
      </b-row>
    </b-container>
  </div>


</template>
<script>
  // Charts
  import LineChart from '@/components/Charts/LineChart';
  import BarChart from '@/components/Charts/BarChart';

  // Components
  import BaseProgress from '@/components/BaseProgress';
  import StatsCard from '@/components/Cards/StatsCard';
  import moving from '@/components/SeatDragAndDrop.vue'

  // Tables
  import SocialTrafficTable from './Dashboard/SocialTrafficTable';
  import PageVisitsTable from './Dashboard/PageVisitsTable';

  export default {
    components: {
      LineChart,
      BarChart,
      BaseProgress,
      StatsCard,
      PageVisitsTable,
      SocialTrafficTable,
      moving
    },
    updated(){


    },
    data() {
      return {
      }

    },
    methods: {

    },
    mounted() {

    }
  };
</script>
<style>
.el-table .cell{
  padding-left: 0px;
  padding-right: 0px;
}

.board{
  background-color :white;
  display : block;
  height : 600px;
  width : 100%;



}
.main{

  height : 100%;
  width : 100%;

}
</style>
