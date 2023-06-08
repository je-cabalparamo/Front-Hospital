<template>
  <v-row class="principal" style="margin: 10px; padding: 10px;">
    <v-col cols="12">
      <v-row class="principal">
        Enfermeros del sistema
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
            <v-text-field v-model="user.nombre" placeholder="Nombre" :rules="rNombre" />
            Apellidos:
            <v-text-field v-model="user.apellidos" placeholder="Apellidos" />
            Planta:
            <v-text-field v-model="user.planta" placeholder="Planta" />
            Ocupacion:
            <v-text-field v-model="user.puesto" placeholder="Puesto" />
            Telefono:
            <v-text-field v-model="user.telefono" placeholder="Telefono" />
            Hora de entrada:
            <v-text-field v-model="user.horarioEntrada" placeholder="Entrada" />
            Hora de salida:
            <v-text-field v-model="user.horarioSalida" placeholder="Salida" />
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
            <v-text-field v-model="nombre" placeholder="Nombre" :rules="rNombre" />
            Apellidos:
            <v-text-field v-model="apellidos" placeholder="Apellidos" />
            Planta:
            <v-text-field v-model="planta" placeholder="Planta" />
            Puesto:
            <v-text-field v-model="puesto" placeholder="Puesto" />
            Telefono:
            <v-text-field v-model="telefono" placeholder="Telefono" />
            Hora de entrada:
            <v-text-field v-model="entrada" placeholder="Entrada" />
            Hora de salida:
            <v-text-field v-model="salida" placeholder="Salida" />
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
      item: [],
      headers: [
        {
          text: 'Nombre',
          sortable: true,
          value: 'nombre'
        },
        {
          text: 'Apellidos',
          sortable: true,
          value: 'apellidos'
        },
        {
          text: 'Correo',
          sortable: true,
          value: 'email'
        },
        {
          text: 'Telefono',
          sortable: true,
          value: 'telefono'
        },
        {
          text: 'Ocupacion',
          sortable: true,
          value: 'puesto'
        },
        {
          text: 'Planta',
          sortable: true,
          value: 'planta'
        },
        {
          text: 'Entrada',
          sortable: true,
          value: 'horarioEntrada'
        },
        {
          text: 'Salida',
          sortable: true,
          value: 'horarioSalida'
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
      correo: '',
      password: '',
      validateEmail: [
        v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
      ],
      rNombre: [v => !v || v.length >= 3 || 'Nombre debe tener minimo 3 caracteres'],
      rPass: [v => !v || v.length > 4 || 'Password minimo 4 caracteres'],
      dialogNovo: false,
      nv: {},
      nombre: '',
      apellidos: '',
      planta: '',
      puesto: '',
      telefono: '',
      entrada: '',
      salida: '',
      email: '',
      pass: ''
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
      await this.$axios.get('/todo', config)
        .then((res) => {
          this.usuarios = res.data.data
          console.log(res)
        })
        .catch((err) => { console.log(err) })
    },
    dialogDelete (item) {
      this.idBorrar = item.email
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
        email: this.idBorrar
      }
      console.log(sendData)
      await this.$axios.post('/eliminar', sendData, config)
        .then((res) => {
          console.log(res)
          if (res.data.alert === 'Success...') {
            this.dialogBorrado = false
            this.loadUsers()
          } else {
            this.dialogBorrado = false
            this.loadUsers()
            // alert(res.data.data)
          }
        })
        .catch((e) => {
          console.log(e)
        })
    },
    dialogU (item) {
      this.correo = item.email
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
      console.log(this.user)
      const userUpdate = {
        nombre: this.user.nombre,
        apellidos: this.user.apellidos,
        planta: this.user.planta,
        puesto: this.user.puesto,
        telefono: this.user.telefono,
        entrada: this.user.horarioEntrada,
        salida: this.user.horarioSalida,
        email: this.correo
      }
      await this.$axios.post('/actualizar', userUpdate, config)
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
        nombre: this.nombre,
        apellidos: this.apellidos,
        planta: this.planta,
        puesto: this.puesto,
        telefono: this.telefono,
        entrada: this.entrada,
        salida: this.salida,
        email: this.email,
        password: this.pass
      }
      console.log(userNovo)
      await this.$axios.post('/insertar', userNovo, config)
        .then((res) => {
          console.log(res)
          this.loadUsers()
          this.dialogNovo = false
          this.nombre = ''
          this.apellidos = ''
          this.planta = ''
          this.puesto = ''
          this.telefono = ''
          this.entrada = ''
          this.salida = ''
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
