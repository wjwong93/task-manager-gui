<template>
  <v-app class="light-blue">
  <v-container class="d-flex align-center fill-height">
    <v-spacer></v-spacer>
    <v-card 
      width="500px"
    >
      <v-card-title class="text-center">Create Account</v-card-title>
      <v-card-subtitle class="text-center">
          Already have an account? Log in <router-link to="/login">here</router-link>.
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
        <v-form
          ref="form"
          v-model="valid"
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
            :rules="[ (v => !!v || 'Enter Password') ]"
            :type="showpw ? 'text' : 'password'"
            :append-icon="showpw ? 'mdi-eye' : 'mdi-eye-off'"
            @click:append="showpw = !showpw"
          ></v-text-field>
          <v-text-field
            required
            v-model="password2"
            outlined
            label="Re-enter Password"
            :disabled="password === ''"
            :rules="[ v => !!v || 'Re-enter Password', v => v == password || 'Passwords do not match' ]"
            :type="showpw2 ? 'text' : 'password'"
            :append-icon="showpw2 ? 'mdi-eye' : 'mdi-eye-off'"
            @click:append="showpw2 = !showpw2"
          ></v-text-field>
          <v-btn color="primary" depressed block :disabled="!valid" @click="registerUser">
            Create Account
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
      valid: false,
      showpw: false,
      showpw2: false,
      username: '',
      password: '',
      password2: '',
      showError: false,
      errorMsg: ''
    }
  },
  methods: {
    validate () {
      return this.$refs.form.validate()
    },
    registerUser() {
      if (!this.validate()) {
        console.log('Validation Failed', this.validate())
        return
      }

      let newUser = new FormData()
      newUser.append('username', this.username)
      newUser.append('password', this.password)
      newUser.append('password2', this.password2)

      axios.post('http://localhost:8000/user/new', newUser, { withCredentials: true }).then(() => {
        
        router.replace({ 'path': '/home' })
      }).catch(err => {
        if (err.response) {
          this.errorMsg = err.response.data.message
          this.showError = true
        }
      })
    }
  }
}
</script>