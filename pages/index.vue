<template>
  <v-container>
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
        <nuxt-link :to="`detailed/${item.id}`">Detailed</nuxt-link>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>

export default {
  async asyncData({$axios}) {

    let resp = await $axios.get('companies', {
      params: {
        page: 1,
        per_page: 15
      }
    })

    let items = resp.data.data
    let total = resp.data.meta.total

    return {
      items,
      headers: [
        {
          value: 'logo',
          text: 'Logo',
          sortable: false
        },
        {
          value: 'name',
          text: 'Name',
          sortable: false
        },
        {
          value: 'email',
          text: 'Email',
          sortable: false
        },
        {
          value: 'website',
          text: 'Website',
          sortable: false
        },
        {
          value: 'employees_count',
          text: 'Total Employees',
          sortable: false
        },
        {
          value: 'action',
          text: 'Action',
          sortable: false
        },
      ],
      loading: false,
      total,
      options: {
        page: 1,
        itemsPerPage: 15
      }
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

      let resp = await this.$axios.get('companies', {
        params: {
          page,
          per_page: itemsPerPage
        }
      })

      this.items = resp.data.data
      this.total = resp.data.meta.total

    }
  },
}
</script>
