<template>
  <v-layout align-start>
    <v-flex>
      <!--Login Module-->
      <v-toolbar flat color="white">
        <v-toolbar-title>Clientes</v-toolbar-title>
        <v-divider class="mx-2" inset vertical></v-divider>
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
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ on }">
            <v-btn color="primary" dark class="mb-2" v-on="on">Nuevo</v-btn>
          </template>
          <v-card>
           <v-card-title>
              <span class="headline">{{ formTitle }}</span>
            </v-card-title>
            <v-card-text>
              <v-container grid-list-md>
                <v-layout wrap>

                  <v-flex xs12 sm12 md12>
                    <v-text-field v-model="nombre" label="Nombre"></v-text-field>
                  </v-flex>

  
                   <v-flex xs12 sm6 md6>
                    <v-select v-model="tipo_documento" :items="documentos" label="Tipo documento"></v-select>
                  </v-flex>

                  <v-flex xs6 sm6 md6>
                    <v-text-field  v-model="num_documento" label="numero documento"></v-text-field>
                  </v-flex>

                  <v-flex xs12 sm12 md12>
                    <v-text-field v-model="direccion" label="Direccion"></v-text-field>
                  </v-flex>

                   <v-flex xs6 sm6 md6>
                    <v-text-field v-model="telefono" label="Telefono"></v-text-field>
                     </v-flex>

                  <v-flex  xs6 sm6 md6>
                    <v-text-field v-model="email" label="Email"></v-text-field>
                  </v-flex>

                  <v-flex xs12 sm12 md12 v-show="valida">
                    <div class="red--text" v-for="v in validaMensaje" :key="v" v-text="v"></div>
                  </v-flex>
                </v-layout>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click.native="close">Cancelar</v-btn>
              <v-btn color="blue darken-1" text @click.native="guardar">Guardar</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

      </v-toolbar>
      <v-data-table :headers="headers" :items="clientes" :search="search" class="elevation-1">
        <template v-slot:[`item.opciones`]="{ item }">
          <v-icon small class="mr-2" @click="editItem(item)">edit</v-icon>

          <template v-if="item.condicion==true">
            <v-icon small class="mr-2" @click="activarDesactivarMostrar(2,item)" color="red">block</v-icon>
          </template>
          <template v-else>
            <v-icon color="blue" small class="mr-2" @click="activarDesactivarMostrar(1,item)">check</v-icon>
          </template>
        </template>

        <template v-slot:[`item.condicion`]="{ item }">
          <div v-if="item.condicion==true">
            <span class="blue--text">Activo</span>
          </div>
          <div v-else>
            <span class="red--text">Inactivo</span>
          </div>
        </template>

        <template slot="no-data">
          <v-btn color="primary" @click="listar">Resetear</v-btn>
        </template>
      </v-data-table>
    </v-flex>
  </v-layout>
</template>
<script>
import axios from "axios";
export default {
data: () => ({
    clientes: [],
    dialog: false,
    headers: [
      { text: "Opciones", value: "opciones", sortable: false },
      { text: "Nombre", value: "nombre" },
      { text: "Tipor persona", value: "tipo_persona" },
      { text: "Tipo documento", value: "tipo_documento" },
      { text: "# Documento", value: "num_documento", sortable: false },
      { text: "Direccion", value: "direccion", sortable: false },
      { text: "Telefono", value: "telefono", sortable: false },
      { text: "Email", value: "email", sortable: false },
    ],
    search: "",
    editedIndex: -1,
    id: "",
    nombre:"",
    tipo_documento: "",
    documentos:['DNI', 'RUC', 'PASAPORTE','CEDULA'],
    num_documento: "",
    direccion: "",
    telefono: "",
    email: "",

    valida: 0,
    validaMensaje: [],
    adModal: 0,
    adAccion: 0,
    adNombre: 0,
    adId: "",
  }),
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Nuevo cliente" : "Actualizar cliente";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
  },

  created() {
    this.listar();
  },
  methods: {
    listar() {
      let me = this;
      axios
        .get("api/Personas/ListarClientes")
        .then(function (response) {
          //console.log(response);
          me.clientes = response.data;
        })
        .catch(function (error) {
          console.log(error);
        });
    },

    editItem(item) {
      this.id = item.idusuario;
      this.idrol = item.idrol;
      this.nombre = item.nombre;
      this.tipo_documento = item.tipo_documento;
      this.num_documento = item.num_documento;
      this.email = item.email;
      this.direccion = item.direccion;
      this.telefono = item.telefono;
      this.editedIndex = 1;
      this.dialog = true;
    },
    close() {
      this.dialog = false;
      this.limpiar();
    },
    limpiar() {
      this.id = "";
      this.tipo_documento = "";
      this.num_documento = "";
      this.direccion = "";
      this.telefono = "";
      this.email = "";
      this.editedIndex = -1;
    },
    guardar() {
      if (this.validar()) {
        return;
      }
      if (this.editedIndex > -1) {
        //Código para editar
        let me = this;
           axios
          .put("api/Personas/Actualizar", {
            idpersona: me.id,
            tipo_persona: 'Cliente',
            nombre: me.nombre,
            tipo_documento: me.tipo_documento,
            num_documento: me.num_documento,
            direccion: me.direccion,
            telefono: me.telefono,
            email: me.email,
          })
          .then(function (response) {
            me.close();
            me.listar();
            me.limpiar();
          })
          .catch(function (error) {
            console.log(error);
          });
      } else {
        //Código para guardar
        let me = this;
        axios
          .post("api/Personas/Crear", {
            tipo_persona: 'Cliente',
            nombre: me.nombre,
            tipo_documento: me.tipo_documento,
            num_documento: me.num_documento,
            direccion: me.direccion,
            telefono: me.telefono,
            email: me.email,

          })
          .then(function (response) {
            me.close();
            me.listar();
            me.limpiar();
          })
          .catch(function (error) {
            console.log(error);
          });
      }
    },
    validar() {
      this.valida = 0;
      this.validaMensaje = [];

      if (this.nombre.length<3 || this.nombre.length > 100) {
        this.validaMensaje.push("El nombre debe tener más de 3 caracteres y menos de 100 caracteres.");
      }
    
      if (!this.tipo_documento) {
        this.validaMensaje.push("Seleccione un tipo de documento.");
      }       
      if (this.validaMensaje.length) {
        this.valida = 1;
      }
      return this.valida;
    },
  
  },
};
</script>