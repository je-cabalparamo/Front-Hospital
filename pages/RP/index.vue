<template>
  <tablaUsuarios />
</template>

<script>
import tablaUsuarios from '~/components/registroC/registroCP.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaUsuarios
  },
  layout: 'main'
}
</script>
