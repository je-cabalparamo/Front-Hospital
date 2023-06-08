<template>
  <v-row class="principal" style="margin: 10px; padding: 10px;">
    <v-col cols="12">
      <v-row class="principal">
        Médicos del sistema
      </v-row>
      <v-data-table
        :headers="headers"
        :items="medicos"
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
          Está por eliminar un usuario, ¿está seguro de esta acción?
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="green darken-1" text @click="dialogBorrado = false">
            Cancelar
          </v-btn>
          <v-btn color="red darken-4" text @click="borrar()">
            Aceptar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogUpdate" max-width="400" persistent>
      <v-card>
        <v-card-title>Actualización de datos</v-card-title>
        <v-card-text>
          <v-form ref="frmUpdate">
            Nombre:
            <v-text-field v-model="user.nombre" placeholder="Nombre" :rules="rNombre" />
            Apellido:
            <v-text-field v-model="user.apellido" placeholder="Apellido" />
            Email:
            <v-text-field v-model="user.email" placeholder="Email" :rules="validateEmail" />
            Teléfono:
            <v-text-field v-model="user.telefono" placeholder="Teléfono" />
            Consultorio:
            <v-text-field v-model="user.consultorio" placeholder="Consultorio" />
            Especialidad:
            <v-text-field v-model="user.especialidad" placeholder="Especialidad" />
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
            Apellido:
            <v-text-field v-model="apellido" placeholder="Apellido" />
            Email:
            <v-text-field v-model="email" placeholder="Email" :rules="validateEmail" />
            Password:
            <v-text-field v-model="pass" placeholder="Password" type="password" :rules="rPass" />
            Teléfono:
            <v-text-field v-model="telefono" placeholder="Teléfono" />
            Consultorio:
            <v-text-field v-model="consultorio" placeholder="Consultorio" />
            Especialidad:
            <v-text-field v-model="especialidad" placeholder="Especialidad" />
            Hora de entrada:
            <v-text-field v-model="entrada" placeholder="Entrada" />
            Hora de salida:
            <v-text-field v-model="salida" placeholder="Salida" />
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
      medicos: [],
      item: [],
      headers: [
        {
          text: 'Nombre',
          sortable: true,
          value: 'nombre'
        },
        {
          text: 'Apellido',
          sortable: true,
          value: 'apellido'
        },
        {
          text: 'Email',
          sortable: true,
          value: 'email'
        },
        {
          text: 'Teléfono',
          sortable: true,
          value: 'telefono'
        },
        {
          text: 'Consultorio',
          sortable: true,
          value: 'consultorio'
        },
        {
          text: 'Especialidad',
          sortable: true,
          value: 'especialidad'
        },
        {
          text: 'Hora de entrada',
          sortable: true,
          value: 'horarioEntrada'
        },
        {
          text: 'Hora de salida',
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
        v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'El correo electrónico debe ser válido'
      ],
      rNombre: [v => !v || v.length >= 3 || 'El nombre debe tener al menos 3 caracteres'],
      rPass: [v => !v || v.length > 4 || 'La contraseña debe tener al menos 4 caracteres'],
      dialogNovo: false,
      nv: {},
      nombre: '',
      apellido: '',
      email: '',
      pass: '',
      telefono: '',
      consultorio: '',
      especialidad: '',
      entrada: '',
      salida: ''
    }
  },
  created () {
    this.loadMedicos()
  },
  methods: {
    async loadMedicos () {
      const config = {
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      await this.$axios.get('/traerMedicos', config)
        .then((res) => {
          this.medicos = res.data.data
          console.log(res)
        })
        .catch((err) => {
          console.log(err)
        })
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
      await this.$axios.post('/eliminarMedico', sendData, config)
        .then((res) => {
          console.log(res)
          if (res.data.error === null) {
            this.dialogBorrado = false
            this.loadMedicos()
          } else {
            this.dialogBorrado = false
            this.loadMedicos()
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
        apellido: this.user.apellido,
        consultorio: this.user.consultorio,
        especialidad: this.user.especialidad,
        telefono: this.user.telefono,
        horarioEntrada: this.user.horarioEntrada,
        horarioSalida: this.user.horarioSalida,
        email: this.user.email
      }
      console.log('Actualiza: ', userUpdate)
      await this.$axios.post('/actualizarMedico', userUpdate, config)
        .then((res) => {
          console.log(res)
          this.loadMedicos()
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
        apellido: this.apellido,
        consultorio: this.consultorio,
        especialidad: this.especialidad,
        telefono: this.telefono,
        horarioEntrada: this.entrada,
        horarioSalida: this.salida,
        email: this.email,
        password: this.pass
      }
      console.log(userNovo)
      await this.$axios.post('/insertarMedico', userNovo, config)
        .then((res) => {
          console.log(res)
          this.loadMedicos()
          this.dialogNovo = false
          this.nombre = ''
          this.apellido = ''
          this.consultorio = ''
          this.especialidad = ''
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
