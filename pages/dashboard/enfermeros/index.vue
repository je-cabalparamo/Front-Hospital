<template>
  <tablaEnfermeros />
</template>

<script>
import tablaEnfermeros from '~/components/enfermeros/tabla-enfermeros.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaEnfermeros
  },
  layout: 'main'
}
</script>
