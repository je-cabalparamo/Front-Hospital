<template>
  <tablaMedicos />
</template>

<script>
import tablaMedicos from '~/components/medicos/tabla-medicos.vue'
export default {
  name: 'IndexPage',
  auth: true,
  components: {
    tablaMedicos
  },
  layout: 'main'
}
</script>
