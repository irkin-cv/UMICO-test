<template>
  <section class="search-block">
    <b-field>
      <input
        v-model="currentName"
        v-debounce:500ms="getUsers"
        placeholder="Search..."
        type="search"
        @keyup.enter="search()"
        @keyup.up="decrement()"
        @keyup.down="increment()"
      >
      <p class="control">
        <b-button :disabled="currentName.length > 0 ? false : true" type="is-primary" label="Search" @click="search()" />
      </p>
    </b-field>
    <div v-if="showAutocomplete && currentName.length >= 3">
      <autocomplete ref="autocomplete" :results-data="resultsData" :is-loading="isLoading" />
    </div>
  </section>
</template>

<script>
import Autocomplete from '@/components/Autocomplete'
import { EVENT_SEARCH } from '@/enums/events'
import { getDirective } from 'vue-debounce'
import { SEARCH_ROUTE } from '@/enums/routes'

export default {
  name: 'Search',

  components: {
    Autocomplete
  },
  directives: {
    debounce: getDirective()
  },
  data () {
    return {
      name: '',
      currentName: '',
      disabled: false,
      resultsData: [],
      isLoading: false,
      showAutocomplete: false,
      autocompleteItem: {
        index: -1,
        value: '',
        href: ''
      }
    }
  },
  methods: {
    search () {
      if (this.autocompleteItem.value) {
        window.location.href = this.autocompleteItem.href
      } else {
        this.$emit(EVENT_SEARCH, this.currentName)
        this.showAutocomplete = false
      }
    },
    getUsers () {
      if (this.currentName.length >= 3 && this.currentName !== this.name) {
        this.isLoading = true

        this.$axios.get(SEARCH_ROUTE.replace(':search_word', this.currentName), { params: { limit: 10, after: this.currentName } })
          .then((data) => {
            this.name = this.currentName
            this.resultsData = data.data.data
            this.isLoading = false
            this.showAutocomplete = true
            this.clearAutocomplete()
          })
          .finally(() => { this.isLoading = false })
      }
    },
    select () {
      const inner = this.$refs.autocomplete.$refs.inner
      this.autocompleteItem.value = inner[this.autocompleteItem.index].childNodes[0].innerText
      this.autocompleteItem.href = inner[this.autocompleteItem.index].childNodes[0].href

      inner.map(el => el.classList.remove('active'))
      inner[this.autocompleteItem.index].classList.add('active')
    },
    increment () {
      if (this.$refs.autocomplete.$refs.inner.length > 0) {
        if (this.autocompleteItem.index !== this.$refs.autocomplete.$refs.inner.length - 1) {
          this.autocompleteItem.index++
          this.select()
        }
      }
    },
    decrement () {
      if (this.$refs.autocomplete.$refs.inner.length > 0) {
        if (this.autocompleteItem.index > 0) {
          this.autocompleteItem.index--
          this.select()
        }
      }
    },
    clearAutocomplete () {
      this.autocompleteItem = {
        index: -1,
        value: '',
        href: ''
      }
    }
  }
}
</script>

<style>
.search-block {
  display: flex;
  position: relative;
}
</style>
