
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

<template>
  <div class="d-center">
    <nuxt-link to="/">Back</nuxt-link>

    <h1>Welcome</h1>
    <div class="text-center">
      <label for="csrf_token">Authorization: </label>
      <input id="csrf_token" type="text" name="csrf_token" v-model="csrfToken">

      <br>
      <br>
      <label for="csrf_token">Old Token: </label>
      <input id="req_token" type="text" name="req_token" v-model="showToken" readonly>

      <form @submit.prevent="submitForm">
        <br>
        <button type="submit">Submit</button>
      </form>
    </div>

    <div class="d-center-blass text-center">
      <h1 v-if="responseData">responseData: </h1>
      <table v-if="responseData" style="margin: auto;">
        <thead>
          <tr>
            <th> User ID </th>
            <th> User Name </th>
            <th> Session </th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>{{ responseData.user_id }}</td>
            <td>{{ responseData.user_names }}</td>
            <td>{{ responseData.session }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      responseData: null,
      csrfToken: null,
      showToken: null,
    };
  },
  async mounted() {
    // await this.getUserData(); // First Running
  },
  methods: {
    // async getUserData() {
    //   try {
    //     const response = await axios.get('http://localhost:3000/api/get_user_data', {
    //       headers: {
    //         Authorization: `Bearer ${this.csrfToken}`
    //       }
    //     });
    //     this.responseData = response.data;
    //     this.csrfToken = response.data.csrf_token;
    //   } catch (error) {
    //     console.error('Error fetching data:', error);
    //   }
    // },
    async submitForm() {
      this.showToken = this.csrfToken;
      try {
        // Submit form with CSRF token
        const response = await axios.get('http://localhost:3000/api/get_user_name', {
          headers: {
            Authorization: `Bearer ${this.csrfToken}`
          }
        });
        this.responseData = response.data;
        this.csrfToken = response.data.csrf_token;
        console.log('ResponseData:', this.responseData);
        console.log('ResponseData:', this.responseData);
      } catch (error) {
        console.error('Error submitting form:', error);
      }
    },
  }
};
</script>
