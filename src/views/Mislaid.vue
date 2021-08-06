<template>
  <div>
    <base-header class="pb-6 pb-8 pt-5 pt-md-8 bg-gradient-success">
    </base-header>
    <b-container fluid class="mt--7">
      <b-row class="justify-content-center">
        <b-col lg="12">
          <card header-classes="bg-transparent">
            <h3 slot="header" class="mb-0">유실물 사진</h3>

            <b-card-group deck >
              <b-card
                v-for="bulletin in bulletins"
                :key="bulletin.id"
                :title="bulletin.date"
                :img-src="bulletin.image"
                img-alt="Img"
                img-top
                v-b-modal.modal
                @click="selected = bulletin.id"
                class="cardBox mb-4"
              >
                <p class="card-text cardText">
                    {{ bulletin.description }}
                </p>
              </b-card>
            </b-card-group>
            <div>
              <b-modal id="modal" title="유실물" class="modal" :hide-footer="true">
                <p class="my-4 modalImageBox">
                  <img :src="bulletins[selected].image" alt="" class="modalImage"/>
                </p>
                  {{ bulletins[selected].description }}
              </b-modal>
            </div>
            <div slot="footer">
              <b-button v-if="adminId ==userId" v-b-modal.createCard variant="primary" block  type="submit" class="mislaidCreateBtn">
                Add
                <b-modal id="createCard" size="xl" title="유실물 게시글 업로드" hide-footer>
                  <div class="createModal">
                    <b-form @submit="onSubmit" @reset="onReset" >
                      <b-form-group
                        id="input-group-date"
                        label="습득일"
                        label-for="input-date"
                      >
                      <b-form-datepicker
                        id="input-date"
                        v-model="form.date"
                        :max="max"
                        locale="ko"
                        required
                      ></b-form-datepicker>
                      </b-form-group>
<!--
                      <p>
                        <input type="file" id="imageFile" class="imageFile" accept="image/*" v-on:change="uploadImage" required>
                      </p> -->
                      <!-- <div class="mt-3">Selected file: {{ form.image ? form.image.name : '' }}</div> -->

                      <b-form-group
                        id="input-group-description"
                        label="설명"
                        label-for="input-description"
                      ><b-form-textarea
                        id="input-description"
                        v-model="form.description"
                        placeholder="Enter description"
                        rows="5"
                        no-resize
                        required
                      ></b-form-textarea>
                      </b-form-group>

                      <b-button type="submit" variant="primary">Submit</b-button>
                      <b-button type="reset" variant="danger">Reset</b-button>
                    </b-form>
                    <!-- <b-card class="mt-3" header="Form Data Result">
                      <pre class="m-0">{{ form }}</pre>
                    </b-card> -->
                  </div>
                </b-modal>
              </b-button>
            </div>
          </card>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import BaseHeader from "@/components/BaseHeader";

export default {
  name: "Mislaid",
  components: {
    BaseHeader,
  },
  data() {
    const today = new Date()
    const maxDate = new Date(today.getFullYear(), today.getMonth(), today.getDate())

    return {
      bulletins: [],
      selected: 0,
      form:
      {
        date: '',
        image: '',
        description: '',
      },
      max: maxDate,
      userId : 0,
      adminId : null,
      libId : null,
    };
  },
  mounted(){

    this.userId = this.$store.getters['user/getUserId']
    this.libId = this.$store.getters['library/getLibraryId']

    this.$axios.get(this.$baseUrl + 'admin/'+this.libId)
    .then(res => {

      this.adminId = res.data[0].id
    })

  },
  methods: {
    uploadImage(e){
      let file = e.target.files;
      let reader = new FileReader();

      reader.readAsDataURL(file[0]);
      reader.onload = e => {
        this.form.image = e.target.result;
      }
    },
    onSubmit(event) {
      event.preventDefault()
      // alert(JSON.stringify(this.form))
      this.bulletins.push({
        id: this.bulletins.length,
        image: this.form.image,
        description: this.form.description,
        date: this.form.date
      })
      // reset
      this.form.date = ''
      this.form.image = ''
      this.form.description = ''
    },
    onReset(event) {
      event.preventDefault()
      // Reset our form values
      this.form.date = ''
      this.form.image = ''
      this.form.description = ''
    },

    bulletinUpdate(){
      this.$store.commit('mislaid/updateBulletin', this.bulletins)
    },
  },

  computed: {
  },
  created() {
    this.bulletins = JSON.parse(JSON.stringify(this.$store.getters['mislaid/getBulletin']))
  }
};
</script>
<style>
.b-card-group .card {
  max-width: 25%;
}

.card-deck .card {
  max-width: calc(25% - 30px);
}
.inputfile {
  display: none;
}
.input-plus {
  cursor: pointer;
}

div.card.cardBox {
  min-width: 150px;
  height: 250px;
  display: block;
  overflow: hidden;
  text-overflow: ellipsis;
}

.modalImageBox {
  height: 300px;
}

.modalImageBox .modalImage {
  position: absolute;
  top: 20px;
  left: 10%;
  width: 80%;
  max-height: 50%;
}

#input-date__outer_ {
  padding: 0 0;
  display: flex;
  height: 46px;
}

#input-date__value_ {
  border: 0px;
}

.dropdown-menu, .b-calendar-header, .b-calendar-nav, .b-calendar-grid {
  width: 450px;
}

.mislaidCreateBtn {
  max-width: 100px;
  margin: auto;

}


</style>
