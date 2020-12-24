<template>
  <v-app id="inspire">
    <v-navigation-drawer v-model="drawer" app>
      <v-app id="inspire">
        <v-card class="mx-auto" width="300">
          <v-list>
            <v-list-item
              v-for="([title, icon, ruta], i) in admins"
              :key="i"
              :to="{ name: ruta }"
            >
              <v-list-item-title v-text="title"></v-list-item-title>

              <v-list-item-icon>
                <v-icon v-text="icon"></v-icon>
              </v-list-item-icon>
            </v-list-item>

            <v-list-item @click="salir()">
              <v-list-item-icon>
                <v-icon>mdi-logout</v-icon>
              </v-list-item-icon>

              <v-list-item-title>Salir</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-card>
      </v-app>
    </v-navigation-drawer>

    <v-app-bar app>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>

      <v-toolbar-title>Proyecto Final Ciclo3</v-toolbar-title>
    </v-app-bar>

    <v-main>
      <v-container>
        <router-view />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import swal from "sweetalert";
export default {
  name: "AuthComponen",

  components: {},

  data: () => ({
    //
    drawer: null,
    admins: [
      ["Home", "mdi-home-account", "Home"],
      ["Categorias", "mdi-view-dashboard", "Categoria"],
      ["Articulos", "mdi-basket", "Articulo"],
      ["Usuarios", "mdi-account-multiple-outline", "Usuario"],
    ],
    created() {
      this.store.dispatch("autoLogin");
    },
  }),
  methods: {
    salir() {
      swal("Gracias por visitarnos", "Vuelve pronto");
      this.$store.dispatch("salir");
    },
  },
};
</script>
