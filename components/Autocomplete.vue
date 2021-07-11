<template>
  <div class="autocomplete">
    <ul v-if="resultsData.length > 0">
      <li v-for="(item, index) of resultsData" :key="index" ref="inner">
        <a :href="getUserDetailRoute(item.spa_id, item.name)">
          {{ item.name }}
        </a>
      </li>
    </ul>
    <div v-else>
      Подходящих аккаунтов не найдено.
    </div>

    <b-loading :active="isLoading" :can-cancel="true" :is-full-page="false" />
  </div>
</template>

<script>
import { SHOW_DETAIL_ROUTE } from '@/enums/routes'

export default {
  name: 'Autocomplete',

  components: {

  },
  props: {
    resultsData: {
      type: Array,
      required: true
    },
    isLoading: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    getUserDetailRoute (spaId, name) {
      return SHOW_DETAIL_ROUTE.replace(':spa_id', spaId).replace(':name', name)
    }
  }
}
</script>

<style>
.autocomplete {
  position: absolute;
  width: 100%;
  left: 0;
  top: 40px;
  background: #fff;
  border: 2px solid #c1c1c1;
  z-index: 99;
}
.autocomplete ul li.active {
  background: #bdaceb;
}
</style>
