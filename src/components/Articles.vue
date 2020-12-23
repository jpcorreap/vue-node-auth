<template>
  <div class="card bg-black">
    <div class="d-flex justify-content-center p-2">
    </div>
    <h3 class="card-title">
      <span>{{ art.nombre }}</span>
    </h3>
    <div class="card-body">
      <p class="card-text">{{ art.descripcion }}</p>
      <p class="card-text">
        <span>{{ art.cod }}</span>
      </p>

      <div>
<v-carousel v-model="model">
      <v-carousel-item
        v-for="(articulo, i) in articulos"
        :key="articulo.nombre"
      >
        <v-sheet
          :color="color"
          height="100%"
          tile
        >
          <v-row
            class="fill-height"
            align="center"
            justify="center"
          >
            <div class="display-3">
              Slide {{ i + 1 }}
            </div>
          </v-row>
        </v-sheet>
      </v-carousel-item>
    </v-carousel>

      </div>

    </div>
  </div>
</template>

<script>
export default {
  name: "Articles",
  props: ["art"],
  articulos:[],
  model: 0,
    articulos: [   
      'nombre',
      'descripcion',
      'estado',
      'codigo',
    ],
  methods: {
    list() {
      axios
        .get("http://localhost:3000/api/articulo/list")
        .then((response) => {          
          this.articulos = response.data;
        })
        .catch((error) => {
          console.log(error);
        });

</script>

<style scoped>
.card {
  height: 100%;
}

img {
  text-align: center;
  margin-right: 5px;
  margin-left: 5px;
  border-radius: 500px;
  padding: 25px;
}

span {
  font-weight
: bold;
}
</style>