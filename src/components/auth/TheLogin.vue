<template>
  <v-form>
    <v-row>
      <v-col cols="12">
        <v-text-field
          v-model="login.email"
          label="E-mail"
          outlined
        ></v-text-field>
      </v-col>

      <v-col cols="12">
        <v-text-field
          v-model="login.password"
          :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
          :type="show ? 'text' : 'password'"
          outlined
          label="Contraseña"
          @click:append="show = !show"
        ></v-text-field>
      </v-col>
    </v-row>

    <v-btn
      elevation="9"
      :disabled="!(this.login.password && this.login.email)"
      block
      class="mr-4"
      @click="loginUser"
      >LogIn</v-btn
    >
  </v-form>
</template>


<script>
import axios from "axios";
export default {
  name: "TheLogin",
  data() {
    return {
      show: false,

      login: {
        email: "",
        password: "",
      },

      beforeCreated() {
        this.store.dispatch("autoLogin")
          ? this.$router.push({ name: "Auth" })
          : false;
      },
    };
  },
  methods: {
    loginUser() {
      axios
        .post("http://localhost:3000/api/usuario/login", this.login)
        .then((response) => {
          return response.data;
        })
        .then((data) => {
          this.$store.dispatch("guardarToken", data.tokenReturn);
          this.$router.push({ name: "Auth" });
        })
        .catch((error) => {
          return error;
        });
    },
  },
};
</script>

<style>
</style>