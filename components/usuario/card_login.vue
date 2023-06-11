<template>
  <div>
    <v-card shaped elevation="5" width="500" color="brown-darken-3">
      <v-card-title class="text-center">
        Login main
      </v-card-title>
      <v-card-text>
        <v-form ref="frmLogin">
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
        </v-form>
      </v-card-text>
      <v-card-actions>
        <v-btn block @click="ingresarSistema">
          <v-icon dense style="padding-right: 20px;">
            mdi-account-key
          </v-icon>
          Ingresar
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
      validateEmail: [
        v => !v || /^\w+([.-]?\w+)@\w+([.-]?\w+)(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
      ],
      validatePassword: [
        v => !v || v.length > 6 || 'Password minimo 6 caracteres'
      ],
      dialog: false,
      mensaje: ''
    }
  },
  mounted () {
    if (this.$auth.loggedIn) {
      this.$router.push('/dashboard')
    }
  },
  methods: {
    async ingresarSistema () {
      if (this.email.length === 0 || this.password.length === 0) {
        alert('Error en parametros vacios')
        return
      }
      if (this.$refs.frmLogin.validate()) {
        const senData = {
          email: this.email,
          password: this.password
        }
        await this.$auth.loginWith('local', {
          data: senData
        }).then(async (res) => {
          if (res.data.alert === 'Success') {
            // Save email and table information to localStorage
            localStorage.setItem('loggedInEmail', this.email)
            localStorage.setItem('loggedInTable', res.data.fromTable)
            this.$router.push('/dashboard')
          } else {
            this.$toast.error(res.data.alert)
          }
          console.log(await (res))
        }).catch((error) => {
          this.mensaje = 'Hubo un error'
          this.dialog = true
          setTimeout(() => {
            this.dialog = false
          }, 2000)
          console.log(error)
        })
      } else {
        this.$toast.error('Error en parametros')
      }
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
