<template>
  <tablaUsuarios />
</template>

<script>
import tablaUsuarios from '~/components/tablaE/tablaE.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaUsuarios
  },
  layout: 'main'
}
</script>
