<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <div class="text-center">
        <img id="logo" src="../static/logo-buscador.png" alt="Logo" />
        <v-text-field
          id="search-imput"
          v-model="searchQuery"
          name="search-text-field"
          label="Escribe tu búsqueda"
          outlined
          clearable
          @input="isTyping = true"
        >
        </v-text-field>
        <v-progress-linear v-if="isLoading" indeterminate> </v-progress-linear>
        <v-list three-line>
          <v-list-item
            v-for="result in searchResult"
            :key="result.index"
            :href="result.url"
            target="_blank"
          >
            <v-list-item-content>
              <v-list-item-title> {{ result.name }} </v-list-item-title>
              <v-list-item-subtitle> {{ result.url }} </v-list-item-subtitle>
              <v-list-item-subtitle>
                {{ result.snippet }}
              </v-list-item-subtitle>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </div>
    </v-col>
  </v-row>
</template>

<script>
import _ from 'lodash'
export default {
  data() {
    return {
      searchQuery: '',
      isTyping: false,
      isLoading: false,
      searchResult: [],
    }
  },
  watch: {
    searchQuery: _.debounce(function () {
      this.isTyping = false
    }, 1000),
    isTyping(value) {
      if (!value) {
        this.searchMethod()
      }
    },
  },
  methods: {
    searchMethod() {
      if (this.searchQuery !== '' && this.searchQuery !== null) {
        this.isLoading = true
        const baseUrl = 'https://jeshujunago.azure-api.net/search?q='
        this.$axios
          .get(baseUrl + this.searchQuery.replace(/\s/g, '+'))
          .then((response) => {
            this.isLoading = false
            this.searchResult = response.data.webPages.value
          })
          .catch(function (error) {
            // eslint-disable-next-line no-console
            console.log(error)
          })
      } else {
        this.isLoading = false
      }
    },
  },
}
</script>
<style scoped>
#logo {
  max-width: 68%;
  margin-bottom: 1.5rem;
}
</style>
