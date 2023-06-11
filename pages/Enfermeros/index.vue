<template>
  <cardLogin />
</template>

<script>
import cardLogin from '~/components/usuario/card_loginE.vue'
export default {
  name: 'IndexPage',
  components:
      {
        cardLogin
      },
  layout: 'login'
}
</script>
