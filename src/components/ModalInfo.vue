<template>
  <div class="text-center">
    <v-dialog v-model="dialog" width="500">
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          class="files__version"
          color="blue light"
          dark
          v-bind="attrs"
          v-on="on"
        >
          {{ mainfile }} - {{ version }}
        </v-btn>
      </template>

      <v-card>
        <v-card-title class="headline grey lighten-2">
          Список файлов в пакете
        </v-card-title>

        <v-card-text>
          <ul class="file__list__modal">
            <li v-for="(file, index) in files" :key="index">
              <span>{{ file }}</span>
            </li>
          </ul>
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="dialog = false"> Закрыть </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  props: {
    mainfile: String,
    version: String,
    files: Array,
  },
  data() {
    return {
      dialog: false,
    };
  },
};
</script>

<style>
.files__version {
  width: 100%;
}
.file__list__modal {
  color: rgb(185, 20, 20);
}
</style>