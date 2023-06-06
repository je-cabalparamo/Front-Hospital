<template>
  <v-row class="principal" style="margin: 10px; padding: 10px;">
    <v-col cols="12">
      <v-row class="principal">
        Doctores del sistema
      </v-row>
      <v-row class="principal">
        <v-data-table
          :headers="headers"
          :items="usuarios"
          style="width: 100%;"
        >
          <template #[`item.acciones`]="{ item }">
            <v-row>
              <v-col cols="6">
                <v-btn icon color="blue" @click="dialogU(item)">
                  <v-icon>mdi-human-edit</v-icon>
                </v-btn>
              </v-col>
              <v-col cols="6">
                <v-btn icon color="orange" @click="dialogDelete(item)">
                  <v-icon>mdi-eraser</v-icon>
                </v-btn>
              </v-col>
            </v-row>
          </template>
        </v-data-table>
      </v-row>
    </v-col>
    <v-btn block color="grey" @click="dialogCreate">
      <v-icon>mdi-plus</v-icon>
    </v-btn>
    <v-dialog v-model="dialogBorrado" max-width="290" persistent>
      <v-card>
        <v-card-title class="text-h5">
          Borrado de Usuario
        </v-card-title>
        <v-card-text>
          Esta por eliminar un ususario, ¿Esta seguro de esta acción?
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="green darken-1" text @click="dialogBorrado = false">
            Cancelar
          </v-btn><v-btn color="red darken-4" text @click="borrar()">
            Aceptar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogUpdate" max-width="400" persistent>
      <v-card>
        <v-card-title>Actualizacion de datos</v-card-title>
        <v-card-text>
          <v-form ref="frmUpdate">
            Nombre:
            <v-text-field v-model="user.name" placeholder="Nombre" :rules="rNombre" />
            A. Paterno:
            <v-text-field v-model="user.apater" placeholder="A. Paterno" />
            A. Materno:
            <v-text-field v-model="user.amater" placeholder="A. Materno" />
            Password:
            <v-text-field v-model="password" placeholder="Password" type="password" :rules="rPass" />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red" @click="dialogUpdate = false">
            Cancelar
          </v-btn>
          <v-btn color="blue" @click="update">
            Actualizar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogNovo" max-width="400" persistent>
      <v-card>
        <v-card-title>Nuevo Usuario</v-card-title>
        <v-card-text>
          <v-form ref="frmNovo">
            Nombre:
            <v-text-field v-model="name" placeholder="Nombre" :rules="rNombre" />
            A. Paterno:
            <v-text-field v-model="apater" placeholder="A. Paterno" />
            A. Materno:
            <v-text-field v-model="amater" placeholder="A. Materno" />
            Correo:
            <v-text-field v-model="email" placeholder="Correo" :rule="validateEmail" />
            Password:
            <v-text-field v-model="pass" placeholder="Password" type="password" :rules="rPass" />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red" @click="dialogNovo = false">
            Cancelar
          </v-btn>
          <v-btn color="blue" @click="novo(item)">
            Registrar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
export default {
  data () {
    return {
      usuarios: [],
      headers: [
        {
          text: 'Nombre',
          sortable: true,
          value: 'name'
        },
        {
          text: 'A. Paterno',
          sortable: true,
          value: 'apater'
        },
        {
          text: 'A. Materno',
          sortable: true,
          value: 'amater'
        },
        {
          text: 'Correo',
          sortable: true,
          value: 'email'
        },
        {
          text: 'Fecha Registro',
          sortable: true,
          value: 'date'
        },
        {
          text: 'Acciones',
          sortable: true,
          value: 'acciones'
        }
      ],
      dialogBorrado: false,
      idBorrar: '',
      dialogUpdate: false,
      user: {},
      password: '',
      validateEmail: [
        v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
      ],
      rNombre: [v => !v || v.length >= 3 || 'Nombre debe tener minimo 3 caracteres'],
      rPass: [v => !v || v.length > 4 || 'Password minimo 4 caracteres'],
      dialogNovo: false,
      nv: {}
    }
  },
  created () {
    this.loadUsers()
  },
  methods: {
    async loadUsers () {
      const config = {
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      await this.$axios.get('/user/getallusers', config)
        .then((res) => {
          this.usuarios = res.data.data
          console.log(res)
        })
        .catch((err) => { console.log(err) })
    },
    dialogDelete (item) {
      this.idBorrar = item._id
      this.dialogBorrado = true
    },
    async borrar () {
      const config = {
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const sendData = {
        id: this.idBorrar
      }
      await this.$axios.post('/user/deleteuser', sendData, config)
        .then((res) => {
          console.log(res)
          if (res.data.error === null) {
            this.dialogBorrado = false
            this.loadUsers()
          } else {
            alert(res.data.data)
          }
        })
        .catch((e) => {
          console.log(e)
        })
    },
    dialogU (item) {
      this.user = item
      console.log(this.user)
      this.dialogUpdate = true
    },
    async update () {
      const config = {
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const userUpdate = {
        id: this.user._id,
        name: this.user.name,
        apater: this.user.apater,
        amater: this.user.amater,
        password: this.user.password
      }
      await this.$axios.post('/user/updateuser', userUpdate, config)
        .then((res) => {
          console.log(res)
          this.loadUsers()
          this.dialogUpdate = false
        })
        .catch((err) => {
          console.log(err)
        })
    },
    dialogCreate () {
      this.dialogNovo = true
    },
    async novo () {
      const config = {
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const userNovo = {
        name: this.name,
        apater: this.apater,
        amater: this.amater,
        email: this.email,
        password: this.pass
      }
      console.log(this.nv)
      await this.$axios.post('/user/register', userNovo, config)
        .then((res) => {
          console.log(res)
          this.loadUsers()
          this.dialogNovo = false
          this.name = ''
          this.apater = ''
          this.amater = ''
          this.email = ''
          this.pass = ''
        })
        .catch((err) => {
          console.log(err)
        })
    }
  }
}
</script>

  <style scoped>
  .principal{
    width: 100%;
  }
  </style>
