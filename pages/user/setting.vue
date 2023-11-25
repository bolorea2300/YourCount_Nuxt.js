<template>
  <div>
    <h1 class="title">ユーザー設定</h1>
    <div class="card mb-3">
      <div class="card-content">
        <div class="media">
          <div class="media-content">
            <p class="title is-4">ユーザー名</p>
          </div>
        </div>

        <div class="content">
          <b-field label="ユーザー名">
            <b-input v-model="name"></b-input>
          </b-field>
          <b-button
            type="is-primary"
            @click="update_name()"
            v-bind:disabled="check_name"
            expanded
            >変更</b-button
          >
        </div>
      </div>
    </div>

    <div class="card mb-3">
      <div class="card-content">
        <div class="media">
          <div class="media-content">
            <p class="title is-4">ユーザー名</p>
          </div>
        </div>

        <div class="content">
          <b-field label="旧パスワード">
            <b-input type="password" v-model="old_password" password-reveal>
            </b-input>
          </b-field>

          <b-field
            label="パスワード"
            message="8～24文字かつ半角の英小文字・英大文字・数字を1文字ずつ以上"
          >
            <b-input type="password" v-model="password" password-reveal>
            </b-input>
          </b-field>

          <b-field
            label="パスワード(再入力)"
            message="8～24文字かつ半角の英小文字・英大文字・数字を1文字ずつ以上"
          >
            <b-input
              type="password"
              v-model="password_confirmation"
              password-reveal
            >
            </b-input>
          </b-field>

          <b-button
            type="is-primary"
            @click="update_password()"
            v-bind:disabled="check_password"
            expanded
            >変更</b-button
          >
        </div>
      </div>
    </div>

    <div class="card mb-3">
      <div class="card-content">
        <div class="media">
          <div class="media-content">
            <p class="title is-4">ユーザー削除</p>
          </div>
        </div>

        <div class="content">
          <b-field label="パスワードを入力してください">
            <b-input type="password" v-model="delete_password" password-reveal>
            </b-input>
          </b-field>
          <p class="has-text-danger">
            <b>一度、削除すれば復旧できません。</b>
          </p>
          <b-button
            type="is-danger"
            @click="delete_user()"
            v-bind:disabled="check_delete"
            expanded
            >削除</b-button
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const regex_password = /^(?=.*?[a-z])(?=.*?[A-Z])(?=.*?\d)[a-zA-Z\d]{8,24}$/

export default {
  name: 'UserSetting',
  head() {
    return {
      title: 'ユーザー設定',
      meta: [{ hid: 'robots', name: 'robots', content: 'noindex' }],
    }
  },
  data() {
    return {
      loading: false,
      old_password: '',
      password: '',
      password_confirmation: '',

      delete_password: '',
    }
  },
  async asyncData({ redirect, $axios }) {
    const url = '/api/user'

    try {
      const user = await $axios.$get(url)

      return user
    } catch {
      redirect('/user')
    }
  },
  computed: {
    check_name: function () {
      if (this.name && this.name.length > 1 && this.name.length <= 40) {
        return false
      } else {
        return true
      }
    },
    check_password: function () {
      if (
        regex_password.test(this.password) &&
        this.old_password &&
        this.password &&
        this.password_confirmation &&
        this.password === this.password_confirmation
      ) {
        return false
      } else {
        return true
      }
    },
    check_delete: function () {
      if (this.delete_password) {
        return false
      } else {
        return true
      }
    },
  },
  methods: {
    update_name: async function () {
      this.loading = true
      let formData = new FormData()
      formData.append('name', this.name)

      await this.$axios
        .$post('/api/user/name', formData)
        .then((res) => {
          this.loading = false

          this.$buefy.toast.open({
            message: '成功しました',
            type: 'is-success',
            position: 'is-bottom',
          })
        })
        .catch((err) => {
          this.loading = false
          this.$buefy.toast.open({
            message: '失敗しました',
            type: 'is-danger',
            position: 'is-bottom',
          })
        })
    },
    update_password: async function () {
      this.loading = true
      let formData = new FormData()
      formData.append('old_password', this.old_password)
      formData.append('password', this.password)
      formData.append('password_confirmation', this.password_confirmation)

      await this.$axios
        .$post('/api/user/password', formData)
        .then((res) => {
          this.loading = false

          this.$buefy.toast.open({
            message: '成功しました',
            type: 'is-success',
            position: 'is-bottom',
          })

          this.old_password = ''
          this.password = ''
          this.password_confirmation = ''
        })
        .catch((err) => {
          this.loading = false
          this.$buefy.toast.open({
            message: '失敗しました',
            type: 'is-danger',
            position: 'is-bottom',
          })
        })
    },
    delete_user: async function () {
      this.loading = true
      let formData = new FormData()
      formData.append('password', this.delete_password)

      await this.$axios
        .$post('/api/user/delete', formData)
        .then((res) => {
          this.loading = false

          this.$buefy.toast.open({
            message: '成功しました',
            type: 'is-success',
            position: 'is-bottom',
          })

          location.href = '/'
        })
        .catch((err) => {
          this.loading = false
          this.$buefy.toast.open({
            message: '失敗しました',
            type: 'is-danger',
            position: 'is-bottom',
          })
        })
    },
  },
}
</script>
