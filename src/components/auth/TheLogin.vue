<template>
  <v-layout justify-center>
    <v-card outlined="true" height="370" style="margin-top: 100px">
      <v-card-title>LOGIN</v-card-title>
      <v-card-text>
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
                icon=""
                v-model="login.password"
                :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                :type="show ? 'text' : 'password'"
                outlined
                label="Contraseña"
                @click:append="show = !show"
              ></v-text-field>
            </v-col>
          </v-row>
        </v-form>
      </v-card-text>
      <v-card-actions>
        <v-btn
          color="blue"
          block
          :disabled="!(this.login.password && this.login.email)"
          @click="loginUser"
          ><h5>Login</h5>
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-layout>
</template>


<script>
import swal from "sweetalert";
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
        .post("https://node-p5.herokuapp.com/api/usuario/login", this.login)
        .then((response) => {
          return response.data;
        })
        .then((data) => {
          this.$store.dispatch("guardarToken", data.tokenReturn);
          swal("Muy bien", "Haz ingresado", "success");
          this.$router.push({ name: "Auth" });
        })
        .catch((error) => {
          swal("Ohhhhh noooo!!!", "Revisa tus credenciales", "error");
          return error;
        });
    },
  },
};
</script>

<style>
</style>