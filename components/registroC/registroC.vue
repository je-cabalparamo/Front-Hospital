<template>
  <div>
    <v-card shaped elevation="5" width="500" color="brown-darken-3">
      <v-card-title class="text-center">
        Hospital
      </v-card-title>
      <v-card-title class="text-center">
        Registro de doctores
      </v-card-title>
      <v-card-text>
        <v-form ref="frmLogin">
          <v-container>
            <v-row>
              <v-col cols="12" md="6">
                <v-text-field
                  v-model="puesto"
                  label="Puesto"
                  placeholder="Escribe tu puesto"
                />
                <v-text-field
                  v-model="nombre"
                  label="Nombre"
                  placeholder="Escribe tu nombre"
                />
                <v-text-field
                  v-model="apellido"
                  label="Apellido"
                  placeholder="Escribe tu apellido"
                />
                <v-text-field
                  v-model="planta"
                  label="Planta"
                  placeholder="Escribe tu Consultorio"
                  :rules="validateConsultorio"
                />
                <v-text-field
                  v-model="telefono"
                  label="Telefono"
                  placeholder="Escribe tu telefono"
                />
              </v-col>
              <v-col cols="12" md="6">
                <v-text-field
                  v-model="entrada"
                  label="Hora entrada"
                  placeholder="Escribe tu horario entrada"
                />
                <v-text-field
                  v-model="salida"
                  label="Hora salida"
                  placeholder="Escribe tu horario salida"
                />
                <v-text-field
                  v-model="email"
                  label="Email"
                  placeholder="Escribe tu correo"
                  :rules="validateEmail"
                />
                <v-text-field
                  v-model="password"
                  label="Password"
                  placeholder="Escribe tu password"
                  type="password"
                  :rules="validatePassword"
                />
              </v-col>
            </v-row>
            </v-contariner>
          </v-container>
        </v-form>
      </v-card-text>
      <v-card-actions>
        <v-btn block @click="novo()">
          Registrar
        </v-btn>
      </v-card-actions>
      <v-card-actions>
        <v-btn block @click="prev()">
          Regresar
        </v-btn>
      </v-card-actions>
    </v-card>
    <v-dialog v-model="dialog" width="200" transition="dialog-bottom-transition">
      <div style="width: 100%; height: 100px; background-color: red;">
        <span style="font-size: 20px; font-weight: 800; color: white;">
          {{ mensaje }}
        </span>
      </div>
    </v-dialog>
  </div>
</template>

<script>
export default {
  data () {
    return {
      email: '',
      password: '',
      validateConsultorio: [
        v => !v || v.length > 3 || 'Consultorio debe incluir mas de 3 caracteres'
      ],
      validateEmail: [
        v => !v || /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
      ],
      validatePassword: [
        v => !v || v.length > 6 || 'Password minimo 6 caracteres'
      ],
      dialog: false,
      mensaje: ''
    }
  },
  methods: {
    prev () {
      this.$router.push('/Doctores')
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
        email: this.email,
        password: this.password,
        telefono: this.telefono,
        consultorio: this.planta,
        especialidad: this.puesto,
        horarioEntrada: this.entrada,
        horarioSalida: this.salida
      }
      await this.$axios.post('/insertarMedico', userNovo, config)
        .then((res) => {
          console.log(res)
          this.$router.push('/')
        })
        .catch((err) => {
          console.log(err)
        })
    }
  }
}
</script>

    <style scoped>
    .colorBtn
    {
      background-color: blue !important;
    }
    .v-dialog__container{
      display: flex;
    }
    </style>
