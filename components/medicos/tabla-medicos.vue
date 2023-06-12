<template>
  <v-row class="principal" style="margin: 10px; padding: 10px;">
    <v-col cols="12">
      <v-row class="principal" style="color: black;">
        Médicos del sistema
      </v-row>
      <v-row class="principal">
        <v-data-table
          :headers="headers"
          :items="medicos"
          style="width: 100%; background-color: #6a8085;"
        >
          <template v-if="loggedInEmail === 'director@hospital.com'" #[`item.acciones`]="{ item }">
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
    <v-btn v-if="loggedInEmail === 'director@hospital.com'" block style="background-color: #789dca;" @click="dialogCreate">
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
      <v-card style="background-color: #6a8085;">
        <v-card-title>Actualización de datos</v-card-title>
        <v-card-text>
          <v-form ref="frmUpdate" v-model="formValid">
            <v-container>
              <v-row>
                <v-col cols="12" md="6">
                  Nombre:
                  <v-text-field v-model="updateUser.nombre" placeholder="Nombre" :rules="rNombre" />
                  Apellido:
                  <v-text-field v-model="updateUser.apellido" placeholder="Apellido" />
                  Email:
                  <v-text-field v-model="updateUser.email" placeholder="Email" :rules="validateEmail" />
                  Teléfono:
                  <v-text-field v-model="updateUser.telefono" placeholder="Teléfono" :rules="validateTelefono" />
                </v-col>
                <v-col cols="12" md="6">
                  Consultorio:
                  <v-text-field v-model="updateUser.consultorio" placeholder="Consultorio" />
                  Especialidad:
                  <v-text-field v-model="updateUser.especialidad" placeholder="Especialidad" />
                  Hora de entrada:
                  <v-text-field v-model="updateUser.horarioEntrada" placeholder="Entrada" :rules="validateHoraEntrada" />
                  Hora de salida:
                  <v-text-field v-model="updateUser.horarioSalida" placeholder="Salida" :rules="validateHoraSalida" />
                </v-col>
              </v-row>
            </v-container>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red" @click="dialogUpdate = false">
            Cancelar
          </v-btn>
          <v-btn color="blue" :disabled="!formValid" @click="update">
            Actualizar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogNovo" max-width="400" persistent>
      <v-card style="background-color: #6a8085;">
        <v-card-title>Nuevo Usuario</v-card-title>
        <v-card-text>
          <v-form ref="frmNovo" v-model="formValid">
            <v-container>
              <v-row>
                <v-col cols="12" md="6">
                  Nombre:
                  <v-text-field v-model="nombre" placeholder="Nombre" :rules="rNombre" />
                  Apellido:
                  <v-text-field v-model="apellido" placeholder="Apellido" />
                  Email:
                  <v-text-field v-model="email" placeholder="Email" :rules="validateEmail" />
                  Password:
                  <v-text-field v-model="pass" placeholder="Password" type="password" :rules="validatePassword" />
                  Teléfono:
                  <v-text-field v-model="telefono" placeholder="Teléfono" :rules="validateTelefono" />
                </v-col>
                <v-col cols="12" md="6">
                  Consultorio:
                  <v-text-field v-model="consultorio" placeholder="Consultorio" />
                  Especialidad:
                  <v-text-field v-model="especialidad" placeholder="Especialidad" />
                  Hora de entrada:
                  <v-text-field v-model="entrada" placeholder="Entrada" :rules="validateHoraEntrada" />
                  Hora de salida:
                  <v-text-field v-model="salida" placeholder="Salida" :rules="validateHoraSalida" />
                </v-col>
              </v-row>
            </v-container>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red" @click="dialogNovo = false">
            Cancelar
          </v-btn>
          <v-btn color="blue" :disabled="!formValid" @click="novo(item)">
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
      loggedInEmail: localStorage.getItem('loggedInEmail'),
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
      selectedUser: {},
      updateUser: {},
      formValid: false,
      formUpdtValid: false,
      correo: '',
      password: '',
      validateEmail: [
        v => (!!v && /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v)) || 'E-mail must be valid'
      ],
      requiredRule: [
        v => (!!v) || 'Campo requerido'
      ],
      rNombre: [
        v => (!!v && v.length >= 3) || 'Nombre debe tener mínimo 3 caracteres'
      ],
      validatePassword: [
        v => (!!v && /^(?=.*[A-Z])(?=.*\d)[A-Za-z\d!@#$%^&*()\-_=+{};:,<.>]{8,}$/.test(v)) || 'La contraseña debe tener al menos 8 caracteres y contener al menos una mayúscula y un dígito'
      ],
      validateTelefono: [
        v => (!!v && /^\d{10}$/.test(v)) || 'El teléfono debe tener 10 números'
      ],
      validateHoraEntrada: [
        v => (!!v && /^(0?[1-9]|1[0-2]):[0-5][0-9] [ap]\.m\.$/.test(v)) || 'La hora de entrada debe tener el formato hh:mm a.m.'
      ],
      validateHoraSalida: [
        v => (!!v && /^(0?[1-9]|1[0-2]):[0-5][0-9] [ap]\.m\.$/.test(v)) || 'La hora de salida debe tener el formato hh:mm a.m.'
      ],
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
          this.$toast.success(res.data.alert)
          console.log(res)
          this.dialogBorrado = false
          this.loadMedicos()
        })
        .catch((e) => {
          console.log(e)
        })
    },
    dialogU (item) {
      this.selectedUser = item
      this.updateUser = { ...item }
      console.log(this.selectedUser)
      this.formUpdtValid = true
      this.dialogUpdate = true
    },
    async update () {
      if (this.$refs.frmUpdate.validate()) {
        const config = {
          headers: {
            'Content-Type': 'application/json; charset=UTF-8',
            'Access-Control-Allow-Origin': '*'
          }
        }
        console.log(this.updateUser)
        const userUpdate = {
          nombre: this.updateUser.nombre,
          apellido: this.updateUser.apellido,
          consultorio: this.updateUser.consultorio,
          especialidad: this.updateUser.especialidad,
          telefono: this.updateUser.telefono,
          horarioEntrada: this.updateUser.horarioEntrada,
          horarioSalida: this.updateUser.horarioSalida,
          email: this.updateUser.email
        }
        console.log('Actualiza: ', userUpdate)
        await this.$axios.post('/actualizarMedico', userUpdate, config)
          .then((res) => {
            this.$toast.success(res.data.alert)
            console.log(res)
            this.loadMedicos()
            this.dialogUpdate = false
          })
          .catch((err) => {
            console.log(err)
          })
      } else {
        this.formUpdtValid = false
      }
    },
    dialogCreate () {
      this.nombre = ''
      this.apellido = ''
      this.consultorio = ''
      this.especialidad = ''
      this.telefono = ''
      this.entrada = ''
      this.salida = ''
      this.email = ''
      this.pass = ''
      this.formValid = false
      this.dialogNovo = true
    },
    async novo () {
      if (this.$refs.frmNovo.validate()) {
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
            this.$toast.success(res.data.alert)
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
      } else {
        this.formValid = false
      }
    }
  }
}
</script>

<style scoped>
.principal{
  width: 100%;
}
</style>
