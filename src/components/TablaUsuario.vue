<template>
  <div>
    <v-app>
      <v-data-table
        :headers="headers"
        :items="usuarios"
        sort-by="nombre"
        class="elevation-20"
        :loading="cargando"
        loading-text="Carganado... Por favor espere."
      >
        <template v-slot:top>
          <v-toolbar flat
            ><v-icon>mdi-account-multiple-outline</v-icon>
            <v-toolbar-title>Usuario</v-toolbar-title>
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
                  Agregar Usuario
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
                          v-model="editedItem.nombre"
                          label="Nombre"
                          required
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="7">
                        <v-select
                          v-model="editedItem.rol"
                          ref="Rol"
                          :items="roles"
                          label="Rol"
                          placeholder="Seleccione..."
                          required
                        ></v-select>
                      </v-col>
                      <v-col cols="12">
                        <v-text-field
                          v-model="editedItem.email"
                          label="E-mail"
                          :rules="[rules.email]"
                          required
                        ></v-text-field>
                      </v-col>
                      <template v-if="editedItem.estado === undefined">
                        <v-col cols="12">
                          <v-text-field
                            v-model="editedItem.password"
                            :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                            :rules="[rules.min]"
                            :type="show1 ? 'text' : 'password'"
                            name="input-10-1"
                            label="Introduzca la contraseña"
                            counter
                            @click:append="show1 = !show1"
                          >
                          </v-text-field>
                        </v-col>
                      </template>
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
                      ¿Realmente quiere desactivar el usuario
                      {{ editedItem.nombre }}
                    </p></template
                  >
                  <template v-else
                    ><p>
                      ¿Realmente quiere activar el usuario
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
    roles: ["Administrador", "Vendedor", "Almacenero", "Cliente"],
    show1: false,

    password: "Password",
    rules: {
      min: (v) => v.length >= 6 || "Min 6 characters",
      email: (value) => {
        const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
        return pattern.test(value) || "Invalid e-mail.";
      },
    },

    dialog: false,
    dialogDelete: false,
    headers: [
      { text: "Id", value: "id" },
      {
        text: "Usuario",
        align: "start",
        sortable: true,
        value: "nombre",
      },

      { text: "Email", value: "email" },
      { text: "Rol", value: "rol" },
      { text: "Estado", value: "estado" },
      ,
      { text: "Acción", value: "actions", sortable: false },
    ],

    usuarios: [],
    editedIndex: -1,
    editedItem: {
      nombre: "",
      email: "",
      rol: "",
      estado: undefined,
    },
    defaultItem: {
      nombre: "",
      email: "",
      rol: "",
      estado: undefined,
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "Nuevo usuario" : "Editar usuario";
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
  },

  methods: {
    list() {
      axios
        .get("https://node-p5.herokuapp.com/api/usuario/list")
        .then((response) => {
          this.cargando = false;
          this.usuarios = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },

    editItem(item) {
      this.editedIndex = item.id;

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
          .put("https://node-p5.herokuapp.com/api/usuario/deactivate", {
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
          .put("https://node-p5.herokuapp.com/api/usuario/activate", {
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
          .put("https://node-p5.herokuapp.com/api/usuario/update", {
            id: this.editedItem.id,
            nombre: this.editedItem.nombre,
            email: this.editedItem.email,
            rol: this.editedItem.rol,
          })
          .then((response) => {
            this.list();
          })
          .catch((error) => {
            return error;
          });
      } else {
        axios
          .post("https://node-p5.herokuapp.com/api/usuario/add", {
            nombre: this.editedItem.nombre,
            email: this.editedItem.email,
            password: this.editedItem.password,
            rol: this.editedItem.rol,
            estado: 1,
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
</style>