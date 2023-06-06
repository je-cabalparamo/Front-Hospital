<template>
  <v-row class="principal" style="margin: 10px; padding: 10px;">
    <v-col cols="12">
      <v-row class="principal">
        Pacientes Dentro del Sistema
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
          Eliminando Paciente
        </v-card-title>
        <v-card-text>
          Esta por eliminar un Paciente, ¿Esta seguro de esta acción?
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="green darken-1" text @click="dialogBorrado = false">
            Cancelar
          </v-btn><v-btn color="red darken-4" text @click="borrarPaciente()">
            Aceptar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogUpdate" max-width="400" persistent>
      <v-card>
        <v-card-title>Actualizacion de Paciente</v-card-title>
        <v-card-text>
          <v-form ref="frmUpdate">
            Nombre:
            <v-text-field v-model="user.nombre" placeholder="Nombre" :rules="rNombre" />
            Apellidos:
            <v-text-field v-model="user.apellidos" placeholder="Apellios" />
            Fecha Entrada:
            <v-text-field v-model="user.fechaEntrada" placeholder="Fecha de Entrada" />
            Fecha Salida:
            <v-text-field v-model="user.fechaSalida" placeholder="Fecha de Salida" />
            Habitacion:
            <v-text-field v-model="user.habitacion" placeholder="Habitacion" />
            Medico Encargado
            <v-text-field v-model="user.medicoEncargado" placeholder="Medico Encargado" />
            Telefono de Emergencia:
            <v-text-field v-model="user.telefonoEmergencia" placeholder="Telefono de Emergencia" />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red" @click="dialogUpdate = false">
            Cancelar
          </v-btn>
          <v-btn color="blue" @click="updatePaciente">
            Actualizar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogNovo" max-width="400" persistent>
      <v-card>
        <v-card-title>Nuevo Paciente</v-card-title>
        <v-card-text>
          <v-form ref="frmNovo">
            ID:
            <v-text-field v-model="pacienteid" placeholder="ID del Paciente" :rules="rNombre" />
            Nombre:
            <v-text-field v-model="nombre" placeholder="Nombre" :rules="rNombre" />
            Apellidos:
            <v-text-field v-model="apellidos" placeholder="Apellios" />
            Fecha Entrada:
            <v-text-field v-model="fechaEntrada" placeholder="Fecha de Entrada" />
            Fecha Salida:
            <v-text-field v-model="fechaSalida" placeholder="Fecha de Salida" />
            Habitacion:
            <v-text-field v-model="habitacion" placeholder="Habitacion" />
            Medico Encargado
            <v-text-field v-model="medicoEncargado" placeholder="Medico Encargado" />
            Telefono de Emergencia:
            <v-text-field v-model="telefonoEmergencia" placeholder="Telefono de Emergencia" />
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red" @click="dialogNovo = false">
            Cancelar
          </v-btn>
          <v-btn color="blue" @click="nuevoPaciente(item)">
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
          text: 'PacienteId',
          sortable: true,
          value: 'pacienteid'
        },
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
          text: 'Habitacion',
          sortable: true,
          value: 'habitacion'
        },
        {
          text: 'Telefono de Emergencia',
          sortable: true,
          value: 'telefonoEmergencia'
        },
        {
          text: 'Fecha de Entrada',
          sortable: true,
          value: 'fechaEntrada'
        },
        {
          text: 'Fecha de Salida',
          sortable: true,
          value: 'fechaSalida'
        },
        {
          text: 'Medico Encargado',
          sortable: true,
          value: 'medicoEncargado'
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
      rNombre: [v => !v || v.length >= 3 || 'Nombre debe tener minimo 3 caracteres'],
      dialogNovo: false,
      nv: {}
    }
  },
  created () {
    this.loadPatients()
  },
  methods: {
    async loadPatients () {
      const config = {
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      await this.$axios.get('/todo_Paciente', config)
        .then((res) => {
          this.usuarios = res.data.data
          console.log(res)
        })
        .catch((err) => { console.log(err) })
    },
    dialogDelete (item) {
      this.idBorrar = item.pacienteid
      this.dialogBorrado = true
    },
    async borrarPaciente () {
      const config = {
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const sendData = {
        pacienteid: this.idBorrar
      }
      await this.$axios.post('/eliminar_Paciente', sendData, config)
        .then((res) => {
          console.log(res)
          if (res.data.error === null) {
            this.dialogBorrado = false
            this.loadPatients()
          } else {
            this.dialogBorrado = false
            this.loadPatients()
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
    async updatePaciente () {
      const config = {
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const userUpdate = {
        pacienteid: this.user.pacienteid,
        nombre: this.user.nombre,
        apellidos: this.user.apellidos,
        habitacion: this.user.habitacion,
        telefonoEmergencia: this.user.telefonoEmergencia,
        fechaEntrada: this.user.fechaEntrada,
        fechaSalida: this.user.fechaSalida,
        medicoEncargado: this.user.medicoEncargado
      }
      await this.$axios.post('/actualizar_Paciente', userUpdate, config)
        .then((res) => {
          console.log(res)
          this.loadPatients()
          this.dialogUpdate = false
        })
        .catch((err) => {
          console.log(err)
        })
    },
    dialogCreate () {
      this.dialogNovo = true
    },
    async nuevoPaciente () {
      const config = {
        headers: {
          'Content-Type': 'application/json; charset=UTF-8',
          'Access-Control-Allow-Origin': '*'
        }
      }
      const userNovo = {
        pacienteid: this.pacienteid,
        nombre: this.nombre,
        apellidos: this.apellidos,
        habitacion: this.habitacion,
        telefonoEmergencia: this.telefonoEmergencia,
        fechaEntrada: this.fechaEntrada,
        fechaSalida: this.fechaSalida,
        medicoEncargado: this.medicoEncargado
      }
      console.log(this.nv)
      await this.$axios.post('/insertar_Paciente', userNovo, config)
        .then((res) => {
          console.log(res)
          this.loadPatients()
          this.dialogNovo = false
          this.nombre = ''
          this.pacienteid = ''
          this.nombre = ''
          this.apellidos = ''
          this.fechaEntrada = ''
          this.fechaSalida = ''
          this.habitacion = ''
          this.medicoEncargado = ''
          this.telefonoEmergencia = ''
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
