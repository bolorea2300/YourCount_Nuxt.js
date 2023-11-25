<template>
  <div>
    <h1 class="title" v-text="loading ? '更新中...' : title"></h1>

    <b-field>
      <b-numberinput v-model="number" @input="push()"></b-numberinput>
    </b-field>

    <div class="mt-50">
      <div class="card">
        <div class="card-content">
          <div class="media">
            <div class="media-content">
              <p class="title is-4">削除フォーム</p>
            </div>
          </div>

          <div class="content">
            <b-field label="タイトルを入力してください">
              <b-input v-model="delete_confirm"></b-input>
            </b-field>
            <b-button
              type="is-danger"
              @click="count_delete()"
              v-bind:disabled="delete_confirm != title"
              expanded
              >削除する</b-button
            >
          </div>
        </div>
      </div>
    </div>

    <b-loading
      :is-full-page="true"
      v-model="loading"
      :can-cancel="false"
    ></b-loading>
  </div>
</template>

<script>
export function debounce(fn, wait) {
  let timer
  return function (...args) {
    if (timer) {
      clearTimeout(timer)
    }
    const context = this
    timer = setTimeout(() => {
      fn.apply(context, args)
    }, wait)
  }
}

export default {
  name: 'Count',
  middleware: 'auth',
  head() {
    return {
      title: this.title,
      meta: [{ hid: 'robots', name: 'robots', content: 'noindex' }],
    }
  },
  data() {
    return {
      loading: false,
      delete_confirm: '',
    }
  },
  async asyncData({ redirect, route, $axios }) {
    const id = route.params.id

    const url = '/api/count/' + id

    try {
      const data = await $axios.$get(url)

      return data
    } catch {
      redirect('/user')
    }
  },
  mounted() {},
  computed: {},
  methods: {
    push: debounce(async function () {
      this.loading = true
      let formData = new FormData()
      formData.append('count_id', this.$route.params.id)
      formData.append('number', this.number)

      this.$axios
        .$post('/api/count/update', formData)
        .then((res) => {
          this.loading = false
          console.log('更新後の数字:' + res)
        })
        .catch((err) => {
          this.loading = false
          this.$buefy.toast.open({
            message: '更新に失敗しました',
            type: 'is-danger',
            position: 'is-bottom',
          })
        })
    }, 500),
    count_delete: async function () {
      this.loading
      let formData = new FormData()
      formData.append('count_id', this.$route.params.id)

      await this.$axios
        .$post('/api/count/delete', formData)
        .then((res) => {
          this.loading = false
          this.$router.push('/count')
        })
        .catch((err) => {
          this.loading = false
          this.$buefy.toast.open({
            message: '削除に失敗しました',
            type: 'is-danger',
            position: 'is-bottom',
          })
        })
    },
  },
}
</script>

<style>
.mt-50 {
  margin-top: 50px;
}
</style>
