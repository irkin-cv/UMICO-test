<template>
  <section>
    <b-table
      v-if="data.length > 0"
      :data="data"
      :per-page="25"
      :paginated="true"
      :selected.sync="selected"
      focusable
    >
      <b-table-column v-slot="props" label="#" field="id">
        {{ props.row.spa_id }}
      </b-table-column>
      <b-table-column v-slot="props" label="Имя пользователя" field="name">
        <a :href="getUserDetailRoute(props.row.spa_id, props.row.name)">
          {{ props.row.name }}
        </a>
      </b-table-column>
      <b-table-column v-slot="props" label="Бои" field="battles_count">
        {{ getSummaryBattlesCount(props.row) }}
      </b-table-column>
      <b-table-column v-slot="props" label="Победы" field="wins">
        {{ getSummaryWinsCount(props.row) }}
      </b-table-column>
    </b-table>
  </section>
</template>

<script>
import { SHOW_DETAIL_ROUTE } from '@/enums/routes'

export default {
  name: 'ResultsTable',
  props: {
    data: {
      type: Array,
      default: () => {}
    }
  },
  data () {
    return {
      selected: {}
    }
  },
  computed: {
    isSelected () {
      return this.selected
    }
  },
  methods: {
    getSummaryBattlesCount (data) {
      if (!data.statistics.pve && !data.statistics.pve) {
        return 0
      }
      const buttlesPVP = data.statistics.pve.battles_count ? data.statistics.pve.battles_count : 0
      const buttlesPVE = data.statistics.pve.battles_count ? data.statistics.pve.battles_count : 0
      const summaryBattlesCount = buttlesPVP + buttlesPVE

      return summaryBattlesCount
    },
    getSummaryWinsCount (data) {
      const summaryBattlesCount = this.getSummaryBattlesCount(data)

      if (!data.statistics.pve && !data.statistics.pve) {
        return 0
      }

      const winsPVP = data.statistics.pve.wins ? data.statistics.pve.wins : 0
      const winsPVE = data.statistics.pve.wins ? data.statistics.pve.wins : 0
      const summaryWinsCount = winsPVP + winsPVE

      if (summaryBattlesCount !== 0 && summaryWinsCount !== 0) {
        return this.getSummaryWinsPersent(summaryBattlesCount, summaryWinsCount)
      } else {
        return 0
      }
    },
    getSummaryWinsPersent (summaryBattlesCount, summaryWinsCount) {
      const summaryWinsPersent = Math.round(summaryWinsCount * 100 / summaryBattlesCount) + '%'

      return summaryWinsPersent
    },
    getUserDetailRoute (spaId, name) {
      return SHOW_DETAIL_ROUTE.replace(':spa_id', spaId).replace(':name', name)
    }
  }
}
</script>
