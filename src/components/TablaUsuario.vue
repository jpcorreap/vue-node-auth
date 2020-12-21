<template>
  <div>
    <v-app>
      <v-data-table
        :headers="headers"
        :items="usuarios"
        sort-by="nombre"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-toolbar flat>
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
                >
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
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.nombre"
                          label="Nombre"
                          :rules="textRules"
                          required
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="editedItem.email"
                          :rules="emailRules"
                          label="E-mail"
                          required
                        ></v-text-field>
                      </v-col>
                      <template v-if="editedItem.estado === undefined">
                        <v-col cols="12">
                          <v-text-field
                            v-model="editedItem.password"
                            :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                            :rules="[rules.required, rules.min]"
                            :type="show1 ? 'text' : 'password'"
                            name="input-10-1"
                            label="Introduzca la contraseña"
                            hint="Al menos 8 caracteres"
                            counter
                            @click:append="show1 = !show1"
                          >
                          </v-text-field>
                        </v-col>
                      </template>
                      <v-col cols="12">
                        <v-autocomplete
                          ref="Rol"
                          v-model="editedItem.rol"
                          :rules="[() => !!roles || 'This field is required']"
                          :items="roles"
                          label="Rol"
                          placeholder="Select..."
                          required
                        ></v-autocomplete>
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
            <template v-if="item.estado">mdi-toggle-switch</template>
            <template v-else>mdi-toggle-switch-off-outline</template>
          </v-icon>
        </template>
        <template v-slot:no-data>
          <v-btn color="primary" @click="initialize"> Reset </v-btn>
        </template>
      </v-data-table>
    </v-app>
    <pre>
    {{ $data.categorias }}
  </pre
    >
  </div>
</template>
<script>
import axios from "axios";
export default {
  data: () => ({
    roles: ["Administrador", "Vendedor", "Almacenero", "Cliente"],
    show1: false,
    show2: true,
    show3: false,
    show4: false,
    password: "Password",
    rules: {
      required: (value) => !!value || "Requirida",
      min: (v) => v.length >= 8 || "Min 8 characters",
      emailMatch: () => `The email and password you entered don't match`,
    },
    Nombre: "",
    email: "",
    textRules: [(v) => !!v || "Requerido"],
    emailRules: [
      (v) => !!v || "E-mail is required",
      (v) => /.+@.+\..+/.test(v) || "E-mail must be valid",
    ],
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
        .get("http://localhost:3000/api/usuario/list")
        .then((response) => {
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
          .put("http://localhost:3000/api/usuario/deactivate", {
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
          .put("http://localhost:3000/api/usuario/activate", {
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
          .put("http://localhost:3000/api/usuario/update", {
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
          .post("http://localhost:3000/api/usuario/add", {
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