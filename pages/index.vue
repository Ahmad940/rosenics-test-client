<template>
  <v-container>
    <v-skeleton-loader v-if='fetchLoad' type='card, card-heading' />
    <div v-else>
      <v-row justify='space-between'>
        <v-col cols='1'>
          <p class='display-1'>Users</p>
        </v-col>
        <v-col cols='5'>
          <v-text-field
            v-model='search'
            outlined
            dense
            placeholder='Search...'
            clearable
            append-icon='mdi-magnify'
          />
        </v-col>
      </v-row>
      {{ category }} {{ interest }}
      <v-expansion-panels class='mb-5'>
        <v-expansion-panel>
          <v-expansion-panel-header>
            Filter
            <v-spacer />
          </v-expansion-panel-header>

          <v-expansion-panel-content>
            <v-row>
              <v-col cols='6'>
                <v-select
                  v-model='category'
                  value='all'
                  clearable
                  outlined
                  :items='categoriesFilter'
                  label='Categories'
                />
              </v-col>
              <v-col cols='6'>
                <v-select
                  v-model='interest'
                  outlined
                  clearable
                  :items='interestsFilter'
                  label='Interests'
                />
              </v-col>
            </v-row>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-expansion-panels>

      <v-data-table
        :headers='headers'
        :items='getUsers'
        :search='search'
        :items-per-page='10'
        class='elevation-1'
      ></v-data-table>
    </div>
  </v-container>
</template>

<script>
import { Report } from 'notiflix'

export default {
  name: 'Home',
  data() {
    return {
      fetchLoad: false,
      users: [],
      headers: [
        { text: 'User Name', value: 'userName' },
        { text: 'Full Name', value: 'fullName' },
        { text: 'Email', value: 'email' },
        { text: 'Interest', value: 'interest' },
        { text: 'Years of experience', value: 'yearsOfExperience' },
        { text: 'Category', value: 'category' }
      ],
      categoriesFilter: [],
      interestsFilter: [],
      interest: '',
      category: '',
      search: ''
    }
  },
  async fetch() {
    this.fetchLoad = true
    try {
      this.users = await this.$axios.$get('/all')
      this.categoriesFilter = await this.$axios.$get('/categories')
      this.interestsFilter = await this.$axios.$get('/interests')
      this.fetchLoad = false
    } catch (err) {
      // eslint-disable-next-line no-console
      console.log('err', err.message)
      // eslint-disable-next-line no-console
      console.log('response', err.response)
      Report.failure('Error', 'Something went wrong', 'Ok')
      this.fetchLoad = false
    }
  },
  head: {
    title: 'Home'
  },
  computed: {
    getUsers() {
      return this.users.filter((user) => {
        if (this.interest && this.category)
          return user.interest === this.interest || user.category === this.category
        else if (this.interest && !this.category)
          return user.interest === this.interest
        else if (!this.interest && this.category)
          return user.category === this.category
        return user
      })
    }
  },
  methods: {}
}
</script>
