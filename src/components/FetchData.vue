<script>
import axios from 'axios'

export default {
  data() {
    return {
      apiData: null, // Store the data from the API
      isLoading: false, // Set a loading state when fetching data
      errorMessage: null, // Set an error message when an error occurs
      cancelSource: axios.CancelToken.source(), // Use axios's cancellation feature
      currentPage: 1, // Number of items per page
      itemsPerPage: 5 // Number of items per page
    }
  },

  computed: {
    paginatedData() {
      const start = (this.currentPage - 1) * this.itemsPerPage
      const end = start + this.itemsPerPage
      return this.apiData ? this.apiData.slice(start, end) : []
    },

    totalPages() {
      return this.apiData
        ? Math.ceil(this.apiData.length / this.itemsPerPage)
        : 0
    }
  },

  mounted() {
    this.fetchDataFromAPI()
  },

  methods: {
    async fetchDataFromAPI() {
      const apiUrl = `https://jsonplaceholder.typicode.com/users/`

      this.isLoading = true // Set loading to true before each request
      this.errorMessage = null // Reset the error message before each request

      try {
        const response = await axios.get(apiUrl, {
          cancelToken: this.cancelSource.token // Use axios's cancellation feature
        })

        this.apiData = response.data // Store the data from the API
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
      this.cancelSource.cancel('Request aborted')
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

      <tbody v-for="item in paginatedData" :key="item.id">
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

    <nav
      aria-label="Page navigation example"
      v-if="!isLoading && !errorMessage"
    >
      <ul class="pagination">
        <li class="page-item" :class="{ disabled: currentPage === 1 }">
          <a class="page-link" href="#" @click.prevent="currentPage--"
            >Previous</a
          >
        </li>

        <li
          class="page-item"
          v-for="page in totalPages"
          :key="page"
          :class="{ active: currentPage === page }"
        >
          <a class="page-link" href="#" @click.prevent="currentPage = page">{{
            page
          }}</a>
        </li>

        <li class="page-item" :class="{ disabled: currentPage === totalPages }">
          <a class="page-link" href="#" @click.prevent="currentPage++">Next</a>
        </li>
      </ul>
    </nav>

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
nav {
  display: flex;
  justify-content: center;
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
