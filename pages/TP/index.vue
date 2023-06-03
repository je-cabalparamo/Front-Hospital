<template>
  <tablaUsuarios />
</template>

<script>
import tablaUsuarios from '~/components/tablaP/tablaP.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaUsuarios
  },
  layout: 'main'
}
</script>
