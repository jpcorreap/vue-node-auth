<template>
  <div class="container">
    <v-app>
      <v-data-table
        :headers="headers"
        :items="articulo"
        sort-by="nombre"
        class="elevation-1"
        :loading="cargando"
        loading-text="Carganado... Por favor espere."
      >
        <template v-slot:top>
          <v-toolbar flat
            ><v-icon>mdi-basket</v-icon>
            <v-toolbar-title> Articulo</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  color="primary"
                  dark
                  class="mb-2"
                  v-bind="attrs"
                  v-on="on"
                  ><v-icon>mdi-plus</v-icon>
                  Agregar Articulo
                </v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="headline">{{ formTitle }}</span>
                </v-card-title>

                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="5">
                        <v-text-field
                          v-model="editedItem.codigo"
                          label="Código"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="5">
                        <v-text-field
                          v-model="editedItem.nombre"
                          label="Nombre"
                        ></v-text-field>
                      </v-col>

                      <v-col cols="12">
                        <v-text-field
                          v-model="editedItem.foto"
                          label="Foto"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="5">
                        <v-text-field
                          v-model="editedItem.precio"
                          label="Precio"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="7">
                        <v-select
                          v-model="categoria"
                          label="Categoria"
                          :items="categorias"
                          item-text="nombre"
                          item-value="id"
                          return-object
                        >
                        </v-select>
                      </v-col>
                      <v-col cols="12">
                        <v-textarea
                          v-model="editedItem.descripcion"
                          label="Descripción"
                          auto-grow
                          no-resize
                          counter="250"
                        ></v-textarea>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="close">
                    Cancelar
                  </v-btn>
                  <v-btn color="blue darken-1" text @click="save">
                    Guardar
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
            <v-dialog v-model="dialogDelete" max-width="500px">
              <v-card>
                <v-card-title class="headline">
                  <template v-if="editedItem.estado == 1"
                    ><p>
                      ¿Realmente quiere desactivar el articulo<br />
                      {{ editedItem.nombre }}
                    </p></template
                  >
                  <template v-else
                    ><p>
                      ¿Realmente quiere activar el articulo<br />
                      {{ editedItem.nombre }} ?
                    </p></template
                  >
                </v-card-title>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="closeDelete"
                    >Cancelar</v-btn
                  >
                  <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                    >Si</v-btn
                  >
                  <v-spacer></v-spacer>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
        </template>
        <template v-slot:item.actions="{ item }">
          <v-icon small class="mr-2" @click="editItem(item)">
            mdi-pencil
          </v-icon>
          <v-icon medium @click="deleteItem(item)">
            <template v-if="item.estado">mdi-window-close</template>
            <template v-else>check</template>
          </v-icon>
        </template>
        <template v-slot:no-data>
          <v-btn color="primary" @click="initialize"> Reset </v-btn>
        </template>
      </v-data-table>
    </v-app>
  </div>
</template>
<script>
import axios from "axios";
export default {
  data: () => ({
    cargando: true,
    dialog: false,
    dialogDelete: false,
    headers: [
      { text: "Id", value: "id" },
      { text: "Codigo", value: "codigo" },
      {
        text: "Articulo",
        align: "start",
        sortable: true,
        value: "nombre",
      },
      { text: "Descripción", value: "descripcion" },
      { text: "Categoria", value: "Categoria.nombre" },
      { text: "Precio", value: "precio" },
      { text: "Estado", value: "estado" },
      ,
      { text: "Acción", value: "actions", sortable: false },
    ],

    articulo: [],
    categorias: [],
    categoria: "",

    editedIndex: -1,
    editedItem: {
      nombre: "",
      descripcion: "",
      estado: 0,
      codigo: "",
      foto: "",
      precio: 0,
      CategoriaId: {
        nombre: "",
        id: 0,
      },
    },
    defaultItem: {
      nombre: "",
      descripcion: "",
      estado: 0,
      codigo: "",
      precio: 0,
      foto: "",
      CategoriaId: {
        nombre: "",
        id: 0,
      },
    },

    // editCategoria: {
    //   nombre: "",
    //   id: "",
    // },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Nuevo articulo" : "Editar articulo";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },

  created() {
    this.list();
    this.listCategoria();
  },

  methods: {
    list() {
      axios
        .get("https://node-p5.herokuapp.com/api/articulo/list")
        .then((response) => {
          this.cargando = false;
          this.articulo = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },

    listCategoria() {
      axios
        .get("https://node-p5.herokuapp.com/api/categoria/list")
        .then((response) => {
          this.categorias = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },

    editItem(item) {
      this.editedIndex = item.id;
      this.categoria = item ? item.Categoria : "";
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = item.id;
      this.editedItem = Object.assign({}, item);
      this.dialogDelete = true;
    },

    deleteItemConfirm() {
      if (this.editedItem.estado === 1) {
        axios
          .put("https://node-p5.herokuapp.com/api/articulo/deactivate", {
            id: this.editedItem.id,
          })
          .then((response) => {
            this.list();
          })
          .catch((error) => {
            return error;
          });
      } else {
        axios
          .put("https://node-p5.herokuapp.com/api/articulo/activate", {
            id: this.editedItem.id,
          })
          .then((response) => {
            this.list();
          })
          .catch((error) => {
            return error;
          });
      }
      this.closeDelete();
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.categoria = "";
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    save() {
      if (this.editedIndex > -1) {
        axios
          .put("https://node-p5.herokuapp.com/api/articulo/update", {
            id: this.editedItem.id,
            nombre: this.editedItem.nombre,
            descripcion: this.editedItem.descripcion,
            codigo: this.editedItem.codigo,
            categoriaId: this.categoria.id,
            foto: this.editedItem.foto,
            precio: this.editedItem.precio,
          })
          .then((response) => {
            this.list();
          })
          .catch((error) => {
            return error;
          });
      } else {
        axios
          .post("https://node-p5.herokuapp.com/api/articulo/add", {
            nombre: this.editedItem.nombre,
            descripcion: this.editedItem.descripcion,
            estado: 1,
            codigo: this.editedItem.codigo,
            categoriaId: this.categoria.id,
            foto: this.editedItem.foto,
            precio: this.editedItem.precio,
          })
          .then((response) => {
            this.list();
          })
          .catch((error) => {
            return error;
          });
      }
      this.close();
    },
  },
};
</script>
<style>
.container {
  margin-top: -100px;
}
</style>