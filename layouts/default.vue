<template>
  <v-app>
    <Navbar />
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>

    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
import Navbar from '~/components/Navbar.vue'
export default {
  components: {
    Navbar,
  },
  data() {
    return {
      fixed: true,
    }
  },
}
</script>
