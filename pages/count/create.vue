<template>
  <div>
    <h1 class="title">新規カウント作成</h1>

    <b-field label="タイトル">
      <b-input v-model="title" maxlength="50"></b-input>
    </b-field>

    <b-button
      type="is-primary"
      v-bind:disabled="check"
      @click="create()"
      expanded
      >新規登録</b-button
    >

    <b-loading
      :is-full-page="true"
      v-model="loading"
      :can-cancel="false"
    ></b-loading>
  </div>
</template>

<script>
export default {
  name: 'CountCreate',
  head() {
    return {
      title: '新規カウント作成',
      meta: [{ hid: 'robots', name: 'robots', content: 'noindex' }],
    }
  },
  data() {
    return {
      title: '',
      loading: false,
    }
  },
  computed: {
    check: function () {
      if (
        this.title.length &&
        this.title.length >= 2 &&
        this.title.length <= 50
      ) {
        return false
      } else {
        return true
      }
    },
  },
  methods: {
    create: async function () {
      this.loading = true
      let formData = new FormData()
      formData.append('title', this.title)

      await this.$axios
        .$post('/api/count/create', formData)
        .then((res) => {
          this.loading = false
          this.$router.push('/count/' + res)
        })
        .catch((err) => {
          this.loading = false
          this.$buefy.toast.open({
            message: '作成に失敗しました',
            type: 'is-danger',
            position: 'is-bottom',
          })
        })
    },
  },
}
</script>
