<template>
  <tablaUsuarios />
</template>

<script>
import tablaUsuarios from '~/components/registroC/registroC.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaUsuarios
  },
  layout: 'main'
}
</script>
