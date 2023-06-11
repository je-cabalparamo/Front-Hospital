<template>
  <tablaUsuarios />
</template>

<script>
import tablaUsuarios from '~/components/registroC/registroCE.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaUsuarios
  },
  layout: 'main'
}
</script>
