<template>
  <section class="section">
    <h1 class="has-text-centered">
      Поиск игроков
    </h1>

    <search @search="getResults" />

    <results v-if="resultsData.length > 0" :data="resultsData" />
  </section>
</template>

<script>
import Search from '@/components/Search'
import Results from '@/components/TableResults'
import { SEARCH_ROUTE } from '@/enums/routes'

export default {
  name: 'HomePage',

  components: {
    Search,
    Results
  },
  data () {
    return {
      resultsData: []
    }
  },
  methods: {
    getResults (searchWord) {
      this.$axios.get(SEARCH_ROUTE.replace(':search_word', searchWord))
        .then((data) => {
          this.resultsData = data.data.data
        })
    }
  }
}
</script>

<style>
h1 {
  text-transform: uppercase;
}
</style>
