<template>
  <tablaUsuarios />
</template>

<script>
import tablaUsuarios from '~/components/tablaD/tablaD.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaUsuarios
  },
  layout: 'main'
}
</script>
