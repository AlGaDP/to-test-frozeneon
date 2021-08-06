<template>
  <v-container fluid class="main">
    <v-data-iterator
      :items="items"
      :page.sync="page"
      :search="search"
      hide-default-footer
    >
    <!-- ------------------header&search---------------------- -->
      <template v-slot:header>
        <v-toolbar dark color="blue darken-3" class="mb-1">
          <v-text-field
            v-model="search"
            clearable
            flat
            solo-inverted
            hide-details
            prepend-inner-icon="mdi-magnify"
            label="Search"
          ></v-text-field>
          <button class="btn btn-primary btn-block" v-on:click="sendData()">
            Поиск
          </button>
        </v-toolbar>
      </template>

    <!-- ------------------------file list---------------------------------- -->
      <template v-slot:default>
        <ul class="file__list">
          <li v-for="item in paginatedFiles" :key="item.version">
            <div class="files">
              <ModalInfo
                v-if="isInfoFooter"
                :mainfile="item.mainfile"
                :version="item.version"
                :files="item.files"
              />
            </div>
          </li>
        </ul>

        <v-pagination v-model="page" :length="numberOfPages"> </v-pagination>
      </template>

  <!-- ------------------------footer---------------------------------- -->
      <template v-slot:footer>
        <dir class="dopinfo" v-if="isInfoFooter">
          AUTHOR:<br />
          {{ itemsInfo.author }} <br /><br />
          DESCRIPTION:<br />
          {{ itemsInfo.description }} <br /><br />
          GITHUB:<br />
          {{ itemsInfo.github }} <br /><br />
          HOMEPAGE:<br />
          {{ itemsInfo.homepage }}
        </dir>
      </template>
    </v-data-iterator>
  </v-container>
</template>


<script>
import ModalInfo from "./ModalInfo.vue";

export default {
  data() {
    return {
      search: "",
      page: 1,
      filesPerPage: 10,
      isInfoFooter: false,
      items: [],
      itemsInfo: [],
    };
  },

  components: {
    ModalInfo,
  },

  computed: {
    numberOfPages() {
      return Math.ceil(this.items.length / this.filesPerPage);
    },
    paginatedFiles() {
      let from = (this.page - 1) * this.filesPerPage;
      let to = from + this.filesPerPage;
      return this.items.slice(from, to);
    },
  },

  methods: {

    async sendData() {
      try {
        const response = await (
          await fetch(
            "http://api.jsdelivr.com/v1/jsdelivr/libraries?name=" + this.search
          )
        ).json();
        if (!response.length) {
          this.isInfoFooter = false;
          throw Error("Found nothing");
        }
        this.itemsInfo = response[0];
        this.items = response[0].assets;
        this.isInfoFooter = true;
      } catch (e) {
        console.warn(e.message);
      }
    },

    handleFiles(files) {
      console.log(files, "version");
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.main {
  width: 75%;
}
.file__list {
  text-align: center;
  width: 50%;
  list-style-type: none;
  margin: 0 auto;
}
.files {
  margin: 5px 0px;
  padding: 2px;
  border-radius: 5px;
  border: 1px solid rgb(128, 76, 196);
}
.dopinfo {
  background-color: #1565c0;
  color: white;
  padding: 3px;
}
.btn.btn-primary.btn-block {
  margin-left: 10px;
}
</style>