<template>
  <v-app light>
    <navbar />

    <div class="col-12">
      <child />
    </div>
  </v-app>
</template>

<script>
import Navbar from '~/components/Navbar'

export default {
  name: 'MainLayout',

  components: {
    Navbar
  }
}
</script>
