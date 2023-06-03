<template>
  <v-row class="principal" style="margin: 10px; padding: 10px; display: flex; justify-content: center;">
    <v-col cols="12">
      <v-row class="principal">
        Pacientes del Sistema
      </v-row>
      <v-row class="principal">
        <v-data-table
          :headers="headers"
          :items="pacientes"
          style="width: 100%;"
        >
          <template #[`item.acciones`]="{ item }">
            <v-row>
              <v-col cols="6">
                <v-btn icon color="orange" @click="dialogUpdate(item)">
                  <v-icon>mdi-human-edit</v-icon>
                </v-btn>
              </v-col>
              <v-col cols="6">
                <v-btn icon color="red" @click="dialogDelete(item)">
                  <v-icon>mdi-eraser</v-icon>
                </v-btn>
              </v-col>
            </v-row>
          </template>
        </v-data-table>
      </v-row>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data () {
    return {
      pacientes: [],
      headers: [
        {
          text: 'Id',
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
          text: 'Fecha Entrada',
          sortable: true,
          value: 'fechaEntrada'
        },
        {
          text: 'Fecha Salida',
          sortable: true,
          value: 'fechaSalida'
        },
        {
          text: 'Habitacion',
          sortable: true,
          value: 'habitacion'
        },
        {
          text: 'Medico',
          sortable: true,
          value: 'medicoEncargado'
        },
        {
          text: 'Telefono Emergencia',
          sortable: true,
          value: 'telefonoEmergencia'
        }
      ],
      user: {

      },
      name: '',
      lastname: '',
      email: '',
      password: '',
      number: '',
      dialogRegistro: false,
      reglaNombre: [
        v => !v || v.length >= 3 || 'Name must have min 3 chars'
      ],
      reglaApellidos: [
        v => !v || v.length >= 3 || 'Lastname must have min 3 chars'
      ],
      reglaEmail: [
        v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
      ],
      reglaNumero: [
        v => !v || v.length === 10 || 'Number must have 10 chars'
      ],
      reglaPassword: [
        v => !v || v.length >= 6 || 'Password must have min 6 chars'
      ]
    }
  },
  created () {
    this.loadUsers()
  },
  methods: {
    async loadUsers () {
      const config = {
        headers: {
          'Content-Type': 'application/json;charset=UTF-8',
          'Acces-Control-Allow-Origin': '*'
        }
      }
      await this.$axios.get('/todo_Paciente', config)
        .then((res) => {
          this.pacientes = res.data.data
        })
        .catch((error) => {
          // eslint-disable-next-line no-console
          console.log(error)
        })
    }
  }
}
</script>

<style scoped>
.principal {
  width: 100%;
}
</style>
