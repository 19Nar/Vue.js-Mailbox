<template>
  <v-app :dark="dark">
      <Navbar @isDark="dark = !dark"/>
    <v-content>
      <v-container fluid>
        <v-layout class="my-3" row justify-space-between>
        <Sidebar v-if="error" :text="error" :color="'warning'"/>
          <v-flex md12 lg10 offset-lg1>
            <v-layout row wrap align-center>
              <v-flex xs12 sm6 md6>
                <v-btn @click="loadMails()" color="warning" class="ml-0">
                <v-icon>cached</v-icon>
                Update
              </v-btn>
              </v-flex>
              <v-flex xs12 sm6 md6>
                <v-text-field v-model="search" icon="search" label="Search" single-line hide-details box />
              </v-flex>
            </v-layout>
          </v-flex>
        </v-layout>
        <v-layout align-center justify-center>
          <v-flex md12 lg10 center>
            <EmailDatabase :mails="mails" />
            <p>latestUpload {{ latestUpload }}</p>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import axios from 'axios'
import Navbar from './components/Navbar'
import Sidebar from './components/Sidebar'
import EmailDatabase from './components/EmailDatabase'

export default {
  name: 'App',
  components: {
    Navbar,
    Sidebar,
    EmailDatabase,
  },
  data () {
    return {
      mails: [],
      loading: false,
      dark: true,
      search: '',
      error: null,
      latestUpload: '',
    }
  },
  created() {
    this.loadMails()
  },
  methods: {
    loadMails() {
      this.loading = true
      axios.get(MAIL_URL)
      .then(response => {
        this.loading = false
        this.latestUpload = new Date()
        return this.mails = response.data
      }).catch(() => {
        this.mails = []
        this.error = 'service'
      })
    },
  },
  filters: {
    formatDate: function (value) {
      if (!value) return ''
      let newDate = new Date(value)
      const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' }
      return newDate.toLocaleDateString('en-EN', options)
    }
  }
}

const MAIL_URL = 'https://getsimpleform.com/messages.json?api_token=' + process.env.VUE_APP_SIMPLEFORM_TOKEN
</script>

