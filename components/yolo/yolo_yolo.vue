<template>
  <div class="welcome-page">
    <div v-if="$auth.loggedIn">
      <div class="card">
        <p class="welcome-message">Bienvenido(a), {{ getGreetingMessage() }}</p>
      </div>
      <div class="card-container">
        <div class="card card-medicos">
          <h3 class="card-title">Total Médicos</h3>
          <p class="card-value">{{ totalMedicos }}</p>
        </div>
        <div class="card card-enfermeros">
          <h3 class="card-title">Total Enfermeros</h3>
          <p class="card-value">{{ totalEnfermeros }}</p>
        </div>
        <div class="card card-pacientes">
          <h3 class="card-title">Total Pacientes</h3>
          <p class="card-value">{{ totalPacientes }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      totalMedicos: 0,
      totalEnfermeros: 0,
      totalPacientes: 0
    }
  },
  methods: {
    async fetchData () {
      try {
        const responseMedicos = await this.$axios.get('/traerMedicos')
        this.totalMedicos = responseMedicos.data.data.length
        console.log('Medicos', responseMedicos.data.data.length)
        const responseEnfermeros = await this.$axios.get('/todo')
        this.totalEnfermeros = responseEnfermeros.data.data.length
        console.log('Enfermeros', responseEnfermeros.data.data.length)
        const responsePacientes = await this.$axios.get('/todo_Paciente')
        this.totalPacientes = responsePacientes.data.data.length
        console.log('Pacientes', responsePacientes.data.data.length)
      } catch (error) {
        console.error(error)
      }
    },
    getGreetingMessage () {
      const loggedInEmail = localStorage.getItem('loggedInEmail')
      const loggedInTable = localStorage.getItem('loggedInTable')
      const loggedName = localStorage.getItem('loggedName')

      if (loggedInEmail && loggedInTable) {
        if (loggedInTable === 'Medico') {
          console.log('Tabla: ', loggedInTable, '\nEmail: ', loggedInEmail)
          return ` Médico, \n${loggedName}, ${loggedInEmail}`
        } else if (loggedInTable === 'Enfermeros') {
          return `Enfermero(a), ${loggedName}, ${loggedInEmail}`
        }
      }

      return ''
    }
  },
  mounted () {
    this.fetchData()
  }
}
</script>

<style scoped>
.welcome-page {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 80vh;
}

.card {
  background-color: #fff;
  color: #333;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  margin-bottom: 20px;
  margin-right: 10px;
}

.card-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.card-title {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 10px;
}

.card-value {
  font-size: 24px;
  text-align: center;
}

.card-medicos {
  background-color: #F0F5FF;
}

.card-enfermeros {
  background-color: #F5F5F5;
}

.card-pacientes {
  background-color: #FFEBEE;
}
</style>
