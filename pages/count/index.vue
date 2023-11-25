<template>
  <div>
    <h1 class="title">カウント一覧</h1>
    <draggable v-model="list" @change="order()">
      <transition-group>
        <div
          class="card mb-2"
          v-for="(data, index) in list"
          :key="index"
          draggable
        >
          <div class="card-content">
            <div class="media">
              <div class="media-content">
                <p class="title is-4">
                  <nuxt-link :to="'/count/' + data.id"
                    >{{ data.title }}:{{ data.number }}</nuxt-link
                  >
                </p>
              </div>
            </div>
          </div>
        </div>
      </transition-group>
    </draggable>
  </div>
</template>

<script>
import draggable from 'vuedraggable'

export default {
  name: 'Count',
  head() {
    return {
      title: 'カウント一覧',
      meta: [{ hid: 'robots', name: 'robots', content: 'noindex' }],
    }
  },
  components: {
    draggable,
  },
  data() {
    return {}
  },
  async asyncData({ $axios, route, error }) {
    const page = route.query.page ? route.query.page : 1

    const url = '/api/count/list?page=' + page

    try {
      const list = await $axios.$get(url)

      return { list: list }
    } catch {
      error()
    }
  },
  methods: {
    order: async function () {
      let formData = new FormData()
      formData.append('list', JSON.stringify(this.list))

      await this.$axios
        .$post('/api/order/update', formData)
        .then((res) => {
          console.log(res)
        })
        .catch((err) => {
          this.loading = false
          this.$buefy.toast.open({
            message: '更新に失敗しました',
            type: 'is-danger',
            position: 'is-bottom',
          })
        })
    },
  },
}
</script>
