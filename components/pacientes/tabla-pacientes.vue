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
    <v-btn block style="background-color: #789dca;" @click="dialogCreate">
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
          <v-form ref="frmUpdate" v-model="formValid">
            <v-container>
              <v-row>
                <v-col cols="12" md="6">
                  Nombre:
                  <v-text-field v-model="updateUser.nombre" placeholder="Nombre" :rules="rNombre" />
                  Apellidos:
                  <v-text-field v-model="updateUser.apellidos" placeholder="Apellios" />
                  Fecha Entrada:
                  <v-menu
                    v-model="dateEntradaPicker"
                    :close-on-content-click="false"
                    transition="scale-transition"
                    offset-y
                    class="date-picker-menu"
                  >
                    <template #activator="{ on }">
                      <v-text-field
                        v-model="updateUser.fechaEntrada"
                        placeholder="Fecha de Entrada"
                        :rules="rFechaEntrada"
                        readonly
                        v-on="on"
                      />
                    </template>
                    <v-date-picker v-model="updateUser.fechaEntrada" class="custom-date-picker" @change="dateEntradaPicker = false" />
                  </v-menu>
                  Fecha Salida:
                  <v-menu
                    v-model="dateSalidaPicker"
                    :close-on-content-click="false"
                    transition="scale-transition"
                    offset-y
                    class="date-picker-menu"
                  >
                    <template #activator="{ on }">
                      <v-text-field
                        v-model="updateUser.fechaSalida"
                        placeholder="Fecha de Salida"
                        :rules="rFechaSalida"
                        readonly
                        v-on="on"
                      />
                    </template>
                    <v-date-picker v-model="updateUser.fechaSalida" class="custom-date-picker" @change="dateSalidaPicker = false" />
                  </v-menu>
                </v-col>
                <v-col cols="12" md="6">
                  Habitacion:
                  <v-text-field v-model="updateUser.habitacion" placeholder="Habitacion" />
                  Medico Encargado
                  <v-text-field v-model="updateUser.medicoEncargado" placeholder="Medico Encargado" />
                  Telefono de Emergencia:
                  <v-text-field v-model="updateUser.telefonoEmergencia" placeholder="Telefono de Emergencia" :rules="rTelefonoEmergencia" />
                </v-col>
              </v-row>
            </v-container>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red" @click="dialogUpdate = false">
            Cancelar
          </v-btn>
          <v-btn color="blue" :disabled="!formValid" @click="updatePaciente">
            Actualizar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogNovo" max-width="400" persistent>
      <v-card>
        <v-card-title>Nuevo Paciente</v-card-title>
        <v-card-text>
          <v-form ref="frmNovo" v-model="formValid">
            <v-container>
              <v-row>
                <v-col cols="12" dm="6">
                  ID:
                  <v-text-field v-model="pacienteid" placeholder="ID del Paciente" :rules="rNombre" />
                  Nombre:
                  <v-text-field v-model="nombre" placeholder="Nombre" :rules="rNombre" />
                  Apellidos:
                  <v-text-field v-model="apellidos" placeholder="Apellios" />
                  Habitacion:
                  <v-text-field v-model="habitacion" placeholder="Habitacion" />
                </v-col>
                <v-col cols="12" md="6">
                  Fecha Entrada:
                  <v-menu
                    v-model="showFechaEntradaPicker"
                    :close-on-content-click="false"
                    transition="scale-transition"
                    offset-y
                    class="date-picker-menu"
                  >
                    <template #activator="{ on }">
                      <v-text-field
                        v-model="fechaEntrada"
                        placeholder="Fecha de Entrada"
                        :rules="rFechaEntrada"
                        readonly
                        v-on="on"
                      />
                    </template>
                    <v-date-picker v-model="fechaEntrada" class="custom-date-picker" @change="showFechaEntradaPicker = false" />
                  </v-menu>
                  Fecha Salida:
                  <v-menu
                    v-model="showFechaSalidaPicker"
                    :close-on-content-click="false"
                    transition="scale-transition"
                    offset-y
                    class="date-picker-menu"
                  >
                    <template #activator="{ on }">
                      <v-text-field
                        v-model="fechaSalida"
                        placeholder="Fecha de Salida"
                        :rules="rFechaSalida"
                        readonly
                        v-on="on"
                      />
                    </template>
                    <v-date-picker v-model="fechaSalida" class="custom-date-picker" @change="showFechaSalidaPicker = false" />
                  </v-menu>
                  Medico Encargado
                  <v-text-field v-model="medicoEncargado" placeholder="Medico Encargado" />
                  Telefono de Emergencia:
                  <v-text-field v-model="telefonoEmergencia" placeholder="Telefono de Emergencia" :rules="rTelefonoEmergencia" />
                </v-col>
              </v-row>
            </v-container>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-btn color="red" @click="dialogNovo = false">
            Cancelar
          </v-btn>
          <v-btn color="blue" :disabled="!formValid" @click="nuevoPaciente(item)">
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
      showFechaEntradaPicker: false,
      showFechaSalidaPicker: false,
      usuarios: [],
      item: [],
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
      selectedUser: {},
      updateUser: {
        fechaEntrada: '',
        fechaSalida: ''
      },
      dateEntradaPicker: false,
      dateSalidaPicker: false,
      formValid: false,
      formUpdtValid: false,
      rNombre: [v => (!!v && v.length >= 3) || 'Nombre debe tener minimo 3 caracteres'],
      requiredRule: [v => (!!v) || 'Campo requerido'],
      rFechaEntrada: [v => (!!v && /^\d{4}-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$/.test(v)) || 'Fecha de Entrada debe tener el formato aaaa-mm-dd'],
      rFechaSalida: [v => (!!v && /^\d{4}-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$/.test(v)) || 'Fecha de Salida debe tener el formato aaaa-mm-dd'],
      rTelefonoEmergencia: [v => (!!v && /^\d{10}$/.test(v)) || 'Telefono de Emergencia debe contener 10 números'],
      dialogNovo: false,
      nv: {},
      telefonoEmergencia: '',
      pacienteid: '',
      nombre: '',
      apellidos: '',
      fechaEntrada: '',
      fechaSalida: '',
      habitacion: '',
      medicoEncargado: ''
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
          this.$toast.success(res.data.alert)
          console.log(res)
          this.dialogBorrado = false
          this.loadPatients()
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
    async updatePaciente () {
      if (this.$refs.frmUpdate.validate()) {
        const config = {
          headers: {
            'Content-Type': 'application/json; charset=UTF-8',
            'Access-Control-Allow-Origin': '*'
          }
        }
        const userUpdate = {
          pacienteid: this.updateUser.pacienteid,
          nombre: this.updateUser.nombre,
          apellidos: this.updateUser.apellidos,
          habitacion: this.updateUser.habitacion,
          telefonoEmergencia: this.updateUser.telefonoEmergencia,
          fechaEntrada: this.updateUser.fechaEntrada,
          fechaSalida: this.updateUser.fechaSalida,
          medicoEncargado: this.updateUser.medicoEncargado
        }
        await this.$axios.post('/actualizar_Paciente', userUpdate, config)
          .then((res) => {
            this.$toast.success(res.data.alert)
            console.log(res)
            this.loadPatients()
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
      this.pacienteid = ''
      this.apellidos = ''
      this.fechaEntrada = ''
      this.fechaSalida = ''
      this.habitacion = ''
      this.medicoEncargado = ''
      this.telefonoEmergencia = ''
      this.dialogNovo = true
    },
    async nuevoPaciente () {
      if (this.$refs.frmNovo.validate()) {
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
            this.$toast.success(res.data.alert)
            console.log(res)
            this.loadPatients()
            this.dialogNovo = false
            this.nombre = ''
            this.pacienteid = ''
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
  .v-menu__content--fixed {
    min-width: 290px!important;
  }
  .custom-date-picker .v-picker__body .v-date-picker-table .v-date-picker-table__current .v-btn__content {
    color: #ffffff !important;
  }
  </style>
