<template>
  <div class="wrapper">
    <notifications></notifications>
    <side-bar>
      <template slot="links">
        <sidebar-item
          :link="{
            name: '열람실 좌석 정보',
            path: '/dashboard',
            icon: 'ni ni-tv-2 text-primary',
          }"
        >
        </sidebar-item>

        <sidebar-item
            :link="{
              name: '짐 관리 게시판',
              path: '/mislaid',
              icon: 'ni ni-planet text-blue'
              }"
            >
        </sidebar-item>
        <sidebar-item
              :link="{
                name: '공지사항',
                path: '/noticeBoard',
                icon: 'ni ni-pin-3 text-orange'
              }">
        </sidebar-item>
        <sidebar-item v-if="isAdmin"
              :link="{
                name: '관리자 좌석배치',
                path: '/admin',
                icon: 'ni ni-single-02 text-yellow'
                }">
        </sidebar-item>
      </template>

      <template slot="links-after">
        <b-nav class="navbar-nav mb-md-3">
          <b-nav-item>
               <i class="ni ni-app"></i> <span>총 좌석 :{{this.$store.getters['library/getSeatCount']}}</span>
          </b-nav-item>
          <b-nav-item >
              <i class="ni ni-app text-primary"></i> 이용 가능 좌석 : 5
          </b-nav-item>
          <b-nav-item >
              <i class="ni ni-app text-danger"></i> 이용 좌석 : 40
          </b-nav-item>
        </b-nav>
      </template>
    </side-bar>
    <div class="main-content">
      <dashboard-navbar :type="$route.meta.navbarType"></dashboard-navbar>

      <div @click="$sidebar.displaySidebar(false)">
        <fade-transition :duration="200" origin="center top" mode="out-in">
          <!-- your content here -->
          <router-view></router-view>
        </fade-transition>
      </div>
      <content-footer v-if="!$route.meta.hideFooter"></content-footer>
    </div>
  </div>
</template>
<script>
  /* eslint-disable no-new */
  import PerfectScrollbar from 'perfect-scrollbar';
  import 'perfect-scrollbar/css/perfect-scrollbar.css';

  function hasElement(className) {
    return document.getElementsByClassName(className).length > 0;
  }

  function initScrollbar(className) {
    if (hasElement(className)) {
      new PerfectScrollbar(`.${className}`);
    } else {
      // try to init it later in case this component is loaded async
      setTimeout(() => {
        initScrollbar(className);
      }, 100);
    }
  }

  import DashboardNavbar from './DashboardNavbar.vue';
  import ContentFooter from './ContentFooter.vue';
  import DashboardContent from './Content.vue';
  import { FadeTransition } from 'vue2-transitions';

  export default {
    components: {
      DashboardNavbar,
      ContentFooter,
      DashboardContent,
      FadeTransition
    },
    computed :{

      getCountAll(){

        return 10
      },
      getLibId(){

        return this.$store.getters['library/getLibraryId']
      },
      getUserId(){
        return this.$store.getters["user/getUserId"]
      }


    },
    watch :{

      getUserId(val)
      {
        this.getAdminId()
      },
      getLibId(val){

        this.getAdminId()

      }

    },
    data(){

      return {
        isAdmin : false

      }
    },
    methods: {
      initScrollbar() {
        let isWindows = navigator.platform.startsWith('Win');
        if (isWindows) {
          initScrollbar('sidenav');
        }
      },
      getAdminId(){

        this.$axios.get("http://i5a209.p.ssafy.io:12346/admin/"+this.getLibId)
        .then(res => {
          if(res.data[0].id == this.$store.getters['user/getUserId'])
          {
            this.isAdmin = true
          }
          else
          {
            this.isAdmin = false
          }
        })
        .catch(err => {
          this.isAdmin = false
        })
      }

    },
    mounted() {
      this.initScrollbar()
      this.getAdminId()
    }

  };
</script>
<style lang="scss">
</style>
