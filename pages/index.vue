<style>
.d-center {
  margin: auto;
  width: 50%;
  border: 3px solid green;
  padding: 10px;
}

.d-center-blass {
  margin: auto;
  width: 50%;
  padding: 10px;
}

.text-center {
  text-align: center;
}

</style>

<template class="bg-black">
  <div class="d-center">
    <nuxt-link to="/welcome">Welcome</nuxt-link>

    <div class="d-center-blass">
      <h1>Login</h1>
    </div>
    <form @submit.prevent="login">
      <input type="hidden" name="csrf-token" v-model="csrfToken">
      <div class="d-center-blass text-center">
        <label for="username">Username: </label>
        <input type="text" id="username" name="username" v-model="member.username" required>
      </div>

      <div class="d-center-blass text-center">
        <label for="password">Password: </label>
        <input type="password" id="password" name="password" v-model="member.password" required>
      </div>
      
      <div class="d-center-blass text-center">
        <button type="submit">Login</button>

        <br>
      </div>
    </form>

    <div>
      <pre id="token_res" class="text-center" v-text="AuthToken"></pre>
      <button @click="copyToClipboard">Copy Token</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  fetchOnServer: true,
  components: {},
  data() {
    return {
      member: {
        username: '',
        password: '',
      },
      'csrf-token': '',
      'AuthToken': '',
      responseData: null
    };
  },
  async created() {
    try {
      const response = await axios.get('http://localhost:3000/api/csrf_token_form');
      
      this.responseData = response.data;
      this.csrfToken = response.data.csrf_token;
      console.log(this.responseData);
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  },
  methods: {
    async login() {
      try {
        console.log(this.csrfToken);
        const response = await axios.post('http://localhost:3000/api/login', {
          member: this.member,
          'csrf-token': this.csrfToken
        });

        // token =
        // localStorage.setItem('sessionCookie', this.csrfToken);

        console.log('Login successful:', response.data);
        this.AuthToken = response.data.token
      } catch (error) {
        console.error('Error logging in:', error);
      }

      axios.interceptors.request.use(config => {
        const sessionCookie = localStorage.getItem('sessionCookie');
        if (sessionCookie) {
          config.headers.Cookie = sessionCookie;
        }
        return config;
      });
    },
    copyToClipboard() {
      const tokenElement = document.getElementById('token_res');
      const range = document.createRange();
      range.selectNode(tokenElement);
      window.getSelection().removeAllRanges();
      window.getSelection().addRange(range);
      document.execCommand('copy');
      window.getSelection().removeAllRanges();
      alert('CSRF token copied to clipboard!');
    }

    
  },
}
</script>

<style scoped>
  body {
    background-color: #fff;
    color: rgba(0,0,0,0.8);
  }
  .dark-mode body {
    background-color: #091a28;
    color: #ebf4f1;
  }
  .sepia-mode body {
    background-color: #f1e7d0;
    color: #433422;
  }
</style>
