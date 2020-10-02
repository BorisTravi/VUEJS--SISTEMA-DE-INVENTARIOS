<template>
  <v-layout aling-center justify-center>
    <v-flex xs12 sm8 md6 lg5 x14>
      <v-card>
        <v-toolbar dark color="blue darken-3">
          <v-toolbar-title>Accesso al sistema</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-text-field v-model="email" autofocus color="accent" label="Email" required></v-text-field>
          <v-text-field
            v-model="password"
            type="password"
            color="accent"
            label="contraseña"
            required
          ></v-text-field>
        </v-card-text>
        <v-card-actions class="px-3 pb-3">
          <v-flex text-xs-right>
            <v-btn @click="ingresar" color="primary">Ingresar</v-btn>
          </v-flex>
        </v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      email: "",
      password: "",
    };
  },

  methods: {
    ingresar() {
      axios.post("api/Usuarios/Login", {
        email: this.email,
        password: this.password,
      });
      then((respuesta) => {
        return respuesta.data;
      })
        .then((data) => {
          this.$store.dispatch("guardarToken", data.token);
          this.$router.push({ name: "home" });
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>