<template>
  <v-container>
    <v-card>
      <v-card-text>
        <v-layout>
          <v-flex xs2>
            <img style="max-width: 100px" :src="companyData.logo_url">
          </v-flex>
          <v-flex xs10>
            <p>Company name: {{ companyData.name }}</p>
            <p>Company email: {{ companyData.email }}</p>
            <p>Company website: {{ companyData.website }}</p>
          </v-flex>
        </v-layout>
      </v-card-text>
      <v-card-text>
        <h3>Employees</h3>
      </v-card-text>
      <v-data-table
        :headers="headers"
        :items="items"
        :options.sync="options"
        :server-items-length="total"
        :loading="loading"
        class="elevation-1"
      >
        <template v-slot:item.logo="{item}">
          <img style="max-width: 70px" :src="item.logo_url">
        </template>
        <template v-slot:item.action="{item}">
          <nuxt-link to="detailed">Detailed</nuxt-link>
        </template>
      </v-data-table>
    </v-card>
  </v-container>
</template>

<script>

  export default {
    async asyncData({$axios, route}) {

      let id = route.params.id

      let companiesResp = await $axios.get(`companies/${id}`)

      let employeesResp = await $axios.get('employees', {
        params: {
          page: 1,
          per_page: 15,
          'filter[company_id]': id,
        }
      })

      let items = employeesResp.data.data
      let total = employeesResp.data.meta.total

      return {
        items,
        headers: [
          {
            value: 'first_name',
            text: 'First name',
            sortable: false
          },
          {
            value: 'last_name',
            text: 'Last name',
            sortable: false
          },
          {
            value: 'email',
            text: 'Email',
            sortable: false
          },
          {
            value: 'phone',
            text: 'Phone',
            sortable: false
          },
        ],
        loading: false,
        total,
        options: {
          page: 1,
          itemsPerPage: 15
        },
        companyData: companiesResp.data.data
      }
    },
    watch: {
      options: {
        handler () {
          this.fetchItems()
        },
        deep: true,
      },
    },
    methods: {
      async fetchItems() {

        const { page, itemsPerPage } = this.options

        let resp = await this.$axios.get('employees', {
          params: {
            page,
            per_page: itemsPerPage,
            'filter[company_id]': this.$route.params.id,
          }
        })

        this.items = resp.data.data
        this.total = resp.data.meta.total

      }
    },
  }
</script>
