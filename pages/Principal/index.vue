<template>
  <tablaUsuarios />
</template>

<script>
import tablaUsuarios from '~/components/principalC/principalC.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaUsuarios
  },
  layout: 'main'
}
</script>
