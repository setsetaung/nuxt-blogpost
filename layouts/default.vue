<template>
  <v-app app dark class="hero">
    <v-app-bar
      app
      dark
    >
      <v-toolbar-title>BlogPost</v-toolbar-title>
      <v-spacer />
      <div v-if="user">
        <h5>
          {{ user.displayName }}
          <v-spacer />
          <v-btn @click="logout">
            logout
          </v-btn>
          <v-btn to="/">
            Blog List
          </v-btn>
        </h5>
      </div>
      <div v-else>
        <v-btn to="/login">
          Login
        </v-btn>
        <v-btn to="/signup">
          Sign Up
        </v-btn>
      </div>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer
      :absolute="!fixed"
      app
    >
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
import { signOut } from '@firebase/auth'
import { auth } from '../plugins/firebase'
export default {
  name: 'DefaultLayout',
  middleware: 'authenticated',
  computed: {
    user () {
      return this.$store.state.user
    }
  },
  methods: {
    logout () {
      signOut(auth).then(() => {
        this.$store.dispatch('setUser', null)
      })
      this.$router.push('/')
    }
  }
}
</script>
<style scoped>
.hero {
  background: url('../assets/nuxt.png');
  background-size: cover;
  height: cover;
}
.btn {
  position: fixed;
}
</style>
