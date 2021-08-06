<template>
  <div>
    <base-header class="pb-6 pb-8 pt-5 pt-md-8 bg-gradient-success">
    </base-header>
    <b-container fluid class="mt--7">
      <b-row class="justify-content-center">
        <b-col lg="12">
          <card header-classes="bg-transparent">
            <h3 slot="header" class="mb-0 noticeBoardTitle">공지사항</h3>
            <div>
              <p class="">Current Page: {{ currentPage }}</p>
              <b-table
                :items="notices"
                :fields="fields"
                head-variant="light"
                :per-page="perPage"
                :current-page="currentPage"
                small
                class="noticeBoardNoticeTable"
              >
                <template #cell(title)="data">
                  <router-link :to="{path:'/noticeDetail/' + data.item.id, params:{id : data.item.id}}">
                    {{data.item.title}}
                  </router-link>
                </template>
              </b-table>
            </div>

            <div class="noticeBoardBottomContainer">
              <b-pagination class="noticeBoardPagination"
                v-model="currentPage"
                :total-rows="rows"
                :per-page="perPage"
                aria-controls="my-table"
                align="center"
                use-router
                :link-gen="linkGen"
              ></b-pagination>

              <div class=" noticeBoardControlContainer mt-5">
                <b-form-select
                  v-model="search_selected"
                  :options="search_options"
                  size="sm"
                  class="noticeBoardSearchSelect "
                ></b-form-select>
                <b-form-input
                  id="input-small"
                  class="noticeBoardSearchInput"
                  size="sm"
                  placeholder="검색어를 입력하세요."
                ></b-form-input>
                <b-button class="btn-sm noticeBoardSearchBtn" squared>검색</b-button>
                <b-button v-if="isAdmin" class="btn-sm" squared to="/noticeCreate">글쓰기</b-button>
              </div>
            </div>
          </card>
        </b-col>
      </b-row>
    </b-container>

  </div>
</template>
<script>

  export default {
    watch :{

      getUserId(val)
      {
        this.getAdminId()
      }

    },
    methods: {
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
      },
      linkGen(pageNum) {
        return pageNum === 1 ? '?' : `?page=${pageNum}`
      },
      sortNotices(notices) {
        let sortedNotices = []

        sortedNotices = notices.reduce((acc, cur, idx) => {
          if(cur.isHead==false) acc.unshift(cur);
          return acc;
        }, sortedNotices)

        sortedNotices = notices.reduce((acc, cur, idx) => {
          if(cur.isHead==true) acc.unshift(cur);
          return acc;
        }, sortedNotices)
        return sortedNotices;
      }
    },
    mounted() {

      var libId = this.$store.getters['library/getLibraryId']
      this.getAdminId()

    },
    data() {
      return {
        notices: [],
        user: {
          userType: '',
        },
        fields: [
          {label: '번호', key:'id', tdClass: 'noticeId', thClass: 'text-center w10'},
          {label: '제목', key:'title', tdClass: '', thClass: 'text-center'},
          {label: '조회수', key:'hitCnt', tdClass: 'createdDate', thClass: 'text-center w20'}
        ],
        perPage: 12,
        currentPage: 1,
        search_selected: 'all',
        search_options: [
          { value: 'all', text: '전체' },
          { value: 'title', text: '제목' },
          { value: 'content', text: '내용' }
        ],
        isAdmin : false

      }
    },
    computed: {
      rows() {
        return this.notices.length
      },
      getLibId(){

        return this.$store.getters['library/getLibraryId']
      },
      getUserId(){

        return this.$store.getters["user/getUserId"]
      }
    },
    created() {

      this.$axios.get(this.$baseUrl +'notice/list/'+this.$store.getters['library/getLibraryId'])
      .then(res => {
        this.notices = [];
        res.data.forEach(element => {

          this.notices.push(element)
        })
        this.notices = this.sortNotices(this.notices)



      })


    }
  };
</script>

<style>

div.card-body {
  height: 730px;
  position: relative;
}

.noticeBoardTitle {
  font-size: 1.5em;
}

.noticeBoardNoticeTable {
  /* position: absolute; */
  /* max-width: div.card-body.width; */
}

.noticeBoardBottomContainer {
  width: 70vw;
  position: absolute;
  bottom: 90px;
  right: 0px;
}

.noticeBoardPagination {
}

.noticeBoardControlContainer {
  display: flex;
  position: absolute;
  right: 26px;
}

.noticeBoardSearchSelect {
  width: 70px;
  margin-right: 7px;
}

.noticeBoardSearchInput {
  width: 180px;
  margin-right: 7px;

}

.noticeBoardSearchBtn {
  width: 60px;
  margin-right: 17px;
}

/* table.b-table thead  */
th.w10{ text-align: center; width: 10%; }
th.w20{ width: 20%; }

td.noticeId {
  text-align: center;
}

td.createdDate  {
  text-align: center;
}

</style>
