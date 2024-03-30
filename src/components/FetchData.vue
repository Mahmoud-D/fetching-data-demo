<script>
import axios from 'axios'

export default {
  data() {
    return {
      apiData: null, // Store the data from the API
      isLoading: false, // Set a loading state when fetching data
      errorMessage: null, // Set an error message when an error occurs
      cancelSource: axios.CancelToken.source() // Use axios's cancellation feature
    }
  },
  mounted() {
    this.fetchDataFromAPI()
  },
  methods: {
    async fetchDataFromAPI() {
      const apiUrl = `https://jsonplaceholder.typicode.com/users/`

      this.isLoading = true
      this.errorMessage = null // Reset the error message before each request

      try {
        const response = await axios.get(apiUrl, {
          cancelToken: this.cancelSource.token // Use axios's cancellation feature
        })
        this.apiData = response.data.slice(0, 7) // get only first 7 items
      } catch (error) {
        if (axios.isCancel(error)) {
          console.log('Request canceled:', error.message)
        } else if (!error.response) {
          // Network error
          this.errorMessage = 'Network error: ' + error.message
        } else {
          // The request was made and the server responded with a status code outside of the range of 2xx
          this.errorMessage = 'Error fetching data: ' + error.response.data
        }
      } finally {
        this.isLoading = false
      }
    },
    abortRequest() {
      this.abortController.abort()
    }
  }
}
</script>

<template>
  <div class="cont">
    <p class="table-category">Try Car Users</p>
    <table class="table" v-if="!isLoading && !errorMessage">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Title</th>
          <th scope="col">Name</th>
          <th scope="col">Phone</th>
          <th scope="col">Image</th>
        </tr>
      </thead>
      <tbody v-for="item in apiData" :key="item.id">
        <tr>
          <th scope="row">{{ item.id }}</th>
          <td>{{ item.company.bs }}</td>
          <td>{{ item.name }}</td>
          <td>{{ item.phone }}</td>
          <td>
            <img
              class="rounded"
              :src="`https://picsum.photos/60/30/?image=${item.id}`"
              alt="item.name"
            />
          </td>
        </tr>
      </tbody>
    </table>
    <p class="loading" v-if="isLoading">Loading...</p>
    <p class="error" v-if="errorMessage">{{ errorMessage }}</p>
  </div>
</template>
<style>
.cont {
  width: 80%;
  margin: 0 auto;
  margin-top: 30px;
  color: #333;
}
.table-category {
  font-size: 28px;
  font-weight: bold;
  text-align: center;
  color: #553192;
}

th {
  background-color: #553192 !important;
  color: white !important;
  border-bottom: 1px solid #999 !important;
}
.error {
  color: red;
  font-size: 25px;
  font-weight: bold;
  text-align: center;
}
.loading {
  font-size: 20px;
  font-weight: bold;
  text-align: center;
  color: #553192;
}
</style>
