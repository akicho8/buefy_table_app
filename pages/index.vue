<template lang="pug">
.section
  b-table(
    :data="data"

    backend-sorting
    :default-sort="[sortField, sortOrder]"
    @sort="onSort"
    )

    template(slot-scope="props")
      b-table-column(field="title"      label="Title" sortable v-text="props.row.title")
      b-table-column(field="vote_count" label="Vote"  sortable v-text="props.row.vote_count")
      b-table-column(label="Operation") show, edit
</template>

<script>
import axios from "axios"

export default {
  name: 'HomePage',

  data() {
    return {
      data: [],
      total: 0,
      sortField: "vote_count",
      sortOrder: "desc",
      page: 1,
      perPage: 20,
    }
  },

  methods: {
    onSort(field, order) {
      this.sortField = field
      this.sortOrder = order
      this.loadAsyncData()
    },

    loadAsyncData() {
      const params = [
        'api_key=bb6f51bef07465653c3e553d6ab161a8',
        'language=en-US',
        'include_adult=false',
        'include_video=false',
        `sort_by=${this.sortField}.${this.sortOrder}`,
        `page=${this.page}`
      ].join('&')

      axios.get(`https://api.themoviedb.org/3/discover/movie?${params}`)
        .then(({ data }) => {
          this.data = []
          this.total = data.total_results
          data.results.forEach(item => this.data.push(item))
        })
        .catch((error) => {
          this.data = []
          this.total = 0
          throw error
        })
    },
  },

  mounted() {
    this.loadAsyncData()
  },
}
</script>
