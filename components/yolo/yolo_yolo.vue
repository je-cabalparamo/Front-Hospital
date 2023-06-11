<template>
  <div>
    <v-btn block @click="verAuth">
      Ver Auth
    </v-btn>
    <div v-if="$auth.loggedIn">
      <p>Bienvenido(a), {{ getGreetingMessage() }}</p>
      <v-btn block @click="logout">
        Logout
      </v-btn>
    </div>
  </div>

</template>

<script>
export default {
  methods: {
    getGreetingMessage () {
      const loggedInEmail = localStorage.getItem('loggedInEmail')
      const loggedInTable = localStorage.getItem('loggedInTable')

      if (loggedInEmail && loggedInTable) {
        if (loggedInTable === 'Medico') {
          console.log('Tabla: ', loggedInTable, '\nEmail: ', loggedInEmail)
          return `Médico ${loggedInEmail}`
        } else if (loggedInTable === 'Enfermeros') {
          return `Enfermero(a) ${loggedInEmail}`
        }
      }

      return ''
    },
    verAuth () {
      console.log(this.$auth)
      console.log(this.$auth.loggedIn)
      console.log(this.$auth.user)
    },
    logout () {
      localStorage.removeItem('loggedInEmail')
      localStorage.removeItem('loggedInTable')
      this.$auth.reset()
      this.$router.push('/')
    }
  }
}
</script>

<style scoped>
.colorBtn {
  background-color: blue !important
}
.v-dialog__container {
  display: flex
}
</style>
