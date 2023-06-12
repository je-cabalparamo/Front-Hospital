<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
      style="background-color: #2ca880;"
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
      style="background-color: #789dca;"
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-img :src="require('../assets/HealthCheck.png')" max-height="65%" max-width="3%" contain />
      <v-toolbar-title>{{ title }}</v-toolbar-title>
      <v-spacer />
      <v-btn v-if="$auth.loggedIn" icon @click="logout">
        <v-icon>mdi-logout</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main style="background-color: #b3cf99;">
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer
      :absolute="!fixed"
      app
      style="background-color: #789dca;"
    >
      <span>&copy; Lenguajes modernos de programacion | DICIS Enero-Diciembre {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-home-account',
          title: 'Menu',
          to: '/dashboard'
        },
        {
          icon: 'mdi-doctor',
          title: 'Medicos',
          to: '/dashboard/medicos'
        },
        {
          icon: 'mdi-mother-nurse',
          title: 'Enfermeros',
          to: '/dashboard/enfermeros'
        },
        {
          icon: 'mdi-account-injury-outline',
          title: 'Pácientes',
          to: '/dashboard/pacientes'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Hospital DICIS'
    }
  },
  methods: {
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
h1 {
  font-size: 20px;
}
v-app-bar {
  background-color: #789dca;
}
</style>
