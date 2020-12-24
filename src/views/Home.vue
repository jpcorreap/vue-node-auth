<template>
  <div class="home">
    <br />
    <div class="titleContainer">
      <h1>¡Bienvenido a J&J consulting!</h1>
          <br />
      <h2>Consultores de previsión de ventas para tu empresa, al alcance de un clic.</h2>
    </div>
    <br />
    <div class="post">
      <div v-if="loading" class="loading">
        Cargando...
      </div>

      <div v-if="error" class="error">
        {{ error }}
      </div>

      <div v-if="post" class="content">
        <!-- Inicio de la sección de artículos -->
        <div class="row justify-content-center mb-5" id="team">
          <div class="col mt-5" v-for="(item, i) of post" :key="i">
            <item-component :item="item"></item-component>
          </div>
        </div>
        <!-- Fin de la sección de artículos -->
      </div>
    </div>
  </div>
</template>

<script>
import ItemComponent from "../components/ItemComponent.vue";

export default {
  name: "Home",
  components: {
    ItemComponent
  },
  data () {
    return {
      loading: false,
      post: null,
      error: null
    }
  },
  created () {
    // fetch the data when the view is created and the data is
    // already being observed
    this.fetchData()
  },
  watch: {
    // call again the method if the route changes
    '$route': 'fetchData'
  },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      const fetchedId = this.$route.params.id
      // replace `getPost` with your data fetching util / API wrapper}
      axios
        .get("https://node-p5.herokuapp.com/api/articulo/list")
        .then((response) => {
          this.loading = false;
          this.post = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    }}
};
</script>

<style scoped>
.home {
  min-height: 93vh;
}
.titleContainer {
  padding-left: 50;
}
</style>
