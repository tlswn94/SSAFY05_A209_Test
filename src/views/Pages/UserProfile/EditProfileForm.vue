<template>
  <card>
    <b-row align-v="center" slot="header" >

    </b-row>

    <b-form @submit.prevent="updateProfile">
      <h6 class="heading-small text-muted mb-4">사용자 정보</h6>

      <div class="pl-lg-4">
        <b-row>
          <b-col lg="6">
            <span
            >
            이름 : {{this.$store.getters['user/getUserName']}}
            </span>
          </b-col>

        </b-row>
        <b-row class ="mt-3">
            <b-col lg="6">
            <span
            >
            이메일 : {{this.$store.getters['user/getUserEmail']}}
            </span>
          </b-col>

        </b-row>
        <b-row class ="mt-3">
            <b-col lg="6">
            <span
            >
            벌점횟수 : {{this.paneltyLog.length}}
            </span>
          </b-col>

        </b-row>
      </div>
      <hr class="my-4">

      <!-- Address -->
      <h6 class="heading-small text-muted mb-4">즐겨찾기한 도서관</h6>

      <div class="pl-lg-4">
        <p v-for="(lib,idx) in this.$store.getters['user/getUserLib']" :key =idx @click="moveToLib(lib)">

        <span v-if ="libnames.length >= 1">{{libnames[idx]}}</span> </p>
      </div>

      <hr class="my-4">

      <h6 class="heading-small text-muted mb-4">벌점이력</h6>
      <div class="pl-lg-4 pb-3">
        <p class ="pb-1" v-for="(item,idx) in paneltyLog" :key =idx >{{item}} </p>
      </div>


    </b-form>
  </card>
</template>
<script>
export default {
  data() {
    return {

      paneltyLog : [],
      asyncDataHolder : [],
      names : [],
      libnames : []
    };
  },
  mounted(){
    this.libnames = []
    this.asyncDataHolder = []
    this.name = []
    this.$axios.get(this.$baseUrl + "penaltylog/"+ this.$store.getters['user/getUserId'])
      .then(res => {
        this.paneltyLog = []
        res.data.forEach(element => this.paneltyLog.push(element))
      })
      .catch(err=> {

        console.log(err)
      })
    this.getLibraryName()
    Promise.all(this.asyncDataHolder).then(res =>{

      res.forEach(element => {

        this.libnames.push(element.data.sido + ' '+ element.data.gugun + ' ' +element.data.name)

      })
    })


  },
  computed :{

    getUserId(){

      return this.$store.getters['user/getUserId']
    },



  },
  watch : {

    getUserId(newVal)
    {

      this.$axios.get("http://i5a209.p.ssafy.io:12346/",this.$store.getters['user/getUserId'])
      .then(res => {

        this.$store.commit('user/updateUserId')
      })
    }

  },
  methods: {
     getLibraryName()
    {
      this.$store.getters['user/getUserLib'].forEach(element => {

        this.asyncDataHolder.push(this.$axios.get(this.$baseUrl+'library/search/?id='+element.libraryId))





      })

      // console.log(this.$axios.get(this.$baseUrl+'library/search/?id='+lib.libraryId).then(res=>res.data))
      // return this.$axios.get(this.$baseUrl+'library/search/?id='+lib.libraryId).then(res=>res.data)






    },
    moveToLib(lib)
    {
      this.$axios.get(this.$baseUrl+'library/search/?id='+lib.libraryId)
      .then(res => {
        this.$store.commit('library/updateLibraryName',res.data.name)
        this.$store.commit('library/updateLibraryArea',res.data.sido+' '+res.data.gugun)
        this.$store.commit('library/updateLibraryId',res.data.id)
        this.$router.push("dashboard")

      })





    }

  }
};
</script>
<style>
p{

  font-weight :bolder;
  cursor : pointer

}


</style>
