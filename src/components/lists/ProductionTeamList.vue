<template>
<div class="data-list">
  <div style="overflow: hidden">
    <table class="table table-header" ref="headerWrapper">
      <thead>
        <tr>
          <th class="name">
          {{ $t("people.list.name") }}
          </th>
          <th class="email">
          {{ $t("people.list.email") }}
          </th>
          <th class="phone">
          {{ $t("people.list.phone") }}
          </th>
          <th class="role">
          {{ $t("people.list.role") }}
          </th>
          <th class="actions"></th>
        </tr>
      </thead>
    </table>
  </div>

  <div class="table-body" v-scroll="onBodyScroll" v-if="!isEmpty">
    <table class="table">
      <tbody>
        <tr v-for="entry in entries" :key="entry.id">
          <people-name-cell class="name" :person="entry" />
          <td class="email">{{ entry.email }}</td>
          <td class="phone">{{ entry.phone }}</td>
          <td class="role">{{ $t('people.role.' + entry.role) }}</td>
          <td class="actions"
            v-if="isCurrentUserAdmin"
          >
            <button
              class="button"
              @click="removePerson(entry)"
            >
              {{ $t('main.remove') }}
            </button>
          </td>
          <td class="actions" v-else>
          </td>
        </tr>
      </tbody>
    </table>
  </div>

  <div class="table-body" v-if="isEmpty">
    <p class="has-text-centered mt2" v-if="isEmpty">
      {{ $t('people.empty_team') }}
    </p>
  </div>

  <p class="has-text-centered footer-info">
    {{ entries.length }} {{ $tc('people.persons', entries.length) }}
  </p>
</div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import PeopleNameCell from '../cells/PeopleNameCell'

export default {
  name: 'production-team-list',
  components: {
    PeopleNameCell
  },

  props: [
    'entries'
  ],

  computed: {
    ...mapGetters([
      'isCurrentUserAdmin'
    ]),

    isEmpty () {
      return !this.entries || this.entries.length === 0
    }
  },

  methods: {
    ...mapActions([
      'removePersonFromTeam'
    ]),

    removePerson (person) {
      this.removePersonFromTeam(person)
    },

    onBodyScroll (event, position) {
      this.$refs.headerWrapper.style.left = `-${position.scrollLeft}px`
    }
  }
}
</script>

<style lang="scss" scoped>
.name {
  width: 230px;
  min-width: 230px;
}
.email {
  width: 300px;
  min-width: 300px;
}
.phone {
  width: 140px;
  min-width: 140px;
}
.role {
  width: 125px;
  min-width: 125px;
}
.actions {
  min-width: 100px;
}

.data-list {
  margin-top: 2em;
}
</style>
