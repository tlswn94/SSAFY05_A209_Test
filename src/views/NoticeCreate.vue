<template>
  <div>
    <base-header class="pb-6 pb-8 pt-5 pt-md-8 bg-gradient-success">
    </base-header>

    <b-container fluid class="mt--7">
      <card header-classes="bg-transparent">
        <b-row class="mt-3 mr-4">
          <b-col  class="noticeCreateTypesColumn">
            <span class="noticeCreateTypesLabel noticeCreateFont12">분류</span>
          </b-col>
          <b-col>
            <b-form-select
              v-model="notice.isHead"
              :options="type_options"
              class="noticeCreateTypeSelect"
            ></b-form-select>
          </b-col>
        </b-row>

        <b-row class="mt-3 mr-4">
          <b-col class="noticeCreateTypesColumn">
            <span class="noticeCreateTypesLabel noticeCreateFont12">제목</span>
          </b-col>
          <b-col>
            <b-form-input
              v-model="notice.title"
              class="noticeCreateTitle mr-5"
              required
            ></b-form-input>
            <!-- <b-form-input v-model="notice.title" placeholder="Enter your name"></b-form-input>
    <div class="mt-2">Value: {{ this.notice.title }}</div> -->
          </b-col>
        </b-row>

        <b-row class="mt-3 mr-4">
          <b-col class="noticeCreateTypesColumn">
            <span class="noticeCreateTypesLabel noticeCreateFont12">작성자</span>
          </b-col>
          <b-col>
            <span class="noticeEditWriter noticeEditFont12">{{ this.$store.getters['library/getLibraryName'].split(' ')[this.$store.getters['library/getLibraryName'].split(' ').length -1] }} {{ writerName }}</span>

          </b-col>
        </b-row>

        <!-- <b-row class="mt-3 mr-4">
          <b-col class="noticeCreateTypesColumn">
            <span class="noticeCreateTypesLabel noticeCreateFont12">첨부파일</span>
          </b-col>
          <b-col>
            <b-form-file multiple>
            <template slot="file-name" slot-scope="{ names }" class="justify-content-center">
              <b-badge variant="light">{{ names[0] }}</b-badge>
              <b-badge v-if="names.length > 1" variant="light" class="ml-1">
                + {{ names.length - 1 }} More files
              </b-badge>
            </template>
            </b-form-file>
          </b-col>
        </b-row> -->

        <b-row class="mt-3  mr-4">
          <b-col class="noticeCreateTypesColumn">
          </b-col>
          <b-col>
            <editor class="noticeCreateEditorContext" @update-context="onChangeContext"/>
          </b-col>
        </b-row>

        <b-row class="mt-3 mr-4 justify-content-center">
          <div class="overflow-auto">
            <div class="noticeCreateBtnContainer mt-5">
              <b-button class="" squared to="/noticeBoard">취소</b-button>
              <b-button class="ml-3" squared @click="onSubmit">등록</b-button>
            </div>
          </div>
        </b-row>
      </card>
    </b-container>

  </div>
</template>
<script>
import Editor from '../components/Editor/Editor.vue'
  export default {
    components: {
      Editor,
    },
    data() {
      const today = new Date()
      return {
        writerName : '',
        notice: {
          isHead: false,
          title: "",
          libraryId: 0,
          userId:0,
          content: '',
          hitCnt: 0
        },
        type_options: [
          { value: false, text: '일반' },
          { value: true, text: '중요' }
        ],
        hits: 0,
        // content: "",

      };
    },
    created() {
      this.notice.libraryId = this.$store.getters['library/getLibraryId']
      this.notice.userId = this.$store.getters['user/getUserId']
      this.$axios.get(this.$baseUrl +'user/'+this.notice.userId)
        .then(res => {

          this.writerName = res.data.nickname
        })

    },
    methods: {
      onSubmit(event) {
        event.preventDefault()

        if(this.notice.content.length == 0)
        {

          alert("본문 내용이 없습니다.")
          return
        }
        else if(this.notice.title.length == 0)
        {

          alert("제목이 없습니다.")
          return
        }
        else
        {
          this.$axios.post(this.$baseUrl +'notice',this.notice)
          this.$router.push('noticeBoard')


        }


      },
      onChangeContext(html) {
        this.notice.content = html;
      }
    }
  };
</script>

<style>
div.card-body {
  height: 900px;
}

.noticeCreateTypesLabel {
  margin-left: 40px;
}

.noticeCreateTypesColumn {
  line-height: 46px;
  max-width: 150px;
}

.noticeCreateTypeSelect {
  width: 100px;
  font-size: 1em;
}

.noticeCreateTitle
{
  font-size:1em;
  margin-right: 10%;

}

.noticeCreateWriter {
  line-height: 46px;
}

.noticeCreateContent {
  font-size:1em;
  height: 500px;
}


.noticeCreateEditorContext {
  height: 500px;
  overflow: auto;
}

.noticeCreateBtnContainer {
  display: flex;
  justify-content: center;
}

.noticeCreateFont12 {
  font-family: 'Nanum Gothic', sans-serif;
  font-size: 1.2em;
}


@import url('https://fonts.googleapis.com/css2?family=Nanum+Gothic&display=swap');
</style>

