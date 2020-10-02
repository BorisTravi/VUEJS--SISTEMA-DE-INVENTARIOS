<template>
  <v-layout>
    <v-flex>
      <v-data-table
        :headers="headers"
        :items="Roles"
        :search="search"
        sort-by="calories"
        class="elevation-4"
      >
        <template v-slot:top>
          <v-toolbar flat color="white">
            <v-toolbar-title>Roles</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-text-field
              append-icon="mic"
              class="text-xs-center"
              v-model="search"
              flat
              hide-details
              label="Busqueda"
              prepend-inner-icon="search"
              solo-inverted
            ></v-text-field>
            <v-spacer></v-spacer> 
      
          </v-toolbar>
        </template>
      
        <template v-slot:no-data>
          <v-btn color="primary" @click="listar">Reset</v-btn>
        </template>
      </v-data-table>
    </v-flex>
  </v-layout>
</template>

<script>
import axios from "axios";
export default {
  data: () => ({
    Roles: [],
    dialog: false,
    headers: [
      { text: "Nombre", value: "nombre" },
      { text: "Descripcion", value: "descripcion", sortable: false },
      { text: "Estado", value: "condicion", sortable: false },
    ],
    search: "",

  }),

  computed: {
    
  },

  watch: {
  
  },

  created() {
    this.listar();
  },

  methods: {
    listar() {
      let me = this;
      axios
        .get("api/Roles/listar")
        .then(function (response) {
          //console.log(response);
          me.Roles = response.data;
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },
};
</script>