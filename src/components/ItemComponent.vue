<template>
  <v-card elevation="2" outlined tile>
    <div class="card">
      <img
        :src="item.foto"
        alt="Foto de artículo"
        width="200px"
        height="200px"
      />
    </div>
    <h3>
      <span>{{ item.nombre }}</span>
    </h3>
    <div>
      <p>{{ item.descripcion }}</p>
      <p>
        <span>{{ item.rol }}</span>
      </p>
    </div>
    <div>
      <small class="text-muted">Pertenece a {{ item.Categoria.nombre }}</small> <br />
      <small class="text-muted">Costo: a partir de ${{ item.precio }}</small>
    </div>
  </v-card>
</template>

<script>
export default {
  name: "ItemComponent",
  props: ["item"],
};
</script>


<style scoped>
.card {
  height: 100%;
  align-content: center;
  align-items: center;
  text-align: center;
}
img {
  text-align: center;
  margin-right: 5px;
  margin-left: 5px;
  border-radius: 500px;
  padding: 25px;
}
span {
  font-weight: bold;
}
</style>