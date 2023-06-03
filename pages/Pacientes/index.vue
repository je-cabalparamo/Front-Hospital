<template>
  <cardLogin />
</template>

<script>
import cardLogin from '~/components/usuario/card_loginP.vue'
export default {
  name: 'IndexPage',
  components:
      {
        cardLogin
      },
  layout: 'login'
}
</script>
