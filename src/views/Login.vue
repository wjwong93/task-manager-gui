<template>
  <v-app class="light-blue">
  <v-container class="d-flex align-center fill-height">
    <v-spacer></v-spacer>
    <v-card 
      width="500px"
    >
      <v-card-title class="text-center">Task Manager</v-card-title>
      <v-card-subtitle class="text-center">
          Don't have an account? Create one <router-link to="/register">here</router-link>.
      </v-card-subtitle>

      <v-card-text>
        <v-alert
          v-model="showError"
          dismissible
          dense
          outlined
          text
          type="error"
          border="left"
          icon="mdi-alert-circle-outline"
        >
          {{ errorMsg }}
        </v-alert>
      <br>
      <v-form
        ref="form"
        lazy-validation
      >
          <v-text-field
            required
            v-model="username"
            outlined
            label="Username"
            :rules="[ v => !!v || 'Enter Username' ]"
          ></v-text-field>
          <v-text-field
            required
            v-model="password"
            outlined
            label="Password"
            :rules="[ v => !!v || 'Enter Password' ]"
            :type="showpw ? 'text' : 'password'"
            :append-icon="showpw ? 'mdi-eye' : 'mdi-eye-off'"
            @click:append="showpw = !showpw"
          ></v-text-field>
          <v-btn color="primary" depressed block @click="validate">
            Log in
          </v-btn>
        </v-form>
      </v-card-text>
    </v-card>
    <v-spacer></v-spacer>
  </v-container>
  </v-app>
</template>


<script>
import axios from 'axios'
import router from '../router/index'

export default {
  data() {
    return {
      username: '',
      password: '',
      showpw: false,
      showError: false,
      errorMsg: ''
    }
  },
  created() {
    const loggedIn = this.$cookies.isKey('user-session')
    if (loggedIn) {
      router.replace({ path: '/home' })
    }
  },
  methods: {
    validate() {
      const valid = this.$refs.form.validate()
      if (valid) {
        this.login()
      }
    },
    login() {
      let self = this
      let user = new FormData()

      self.showError = false

      user.append('username', this.username)
      user.append('password', this.password)
      console.log(this.username, this.password)
      axios.post('http://localhost:8000/login', user, { withCredentials: true }).then(function (res) {
        console.log(res.status)
        router.replace({ path: '/home' })
      }).catch(function (err) {
        self.errorMsg = err.response.data.message
        self.showError = true
        // setTimeout(() => { self.showError = false }, 10000)
      })
    }
  }
}
</script>