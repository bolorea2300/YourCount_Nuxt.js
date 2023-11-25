<template>
  <div>
    <h1 class="title">新規登録</h1>

    <div v-if="error">
      <h2 class="subtitle">エラーが発生しました</h2>
    </div>

    <div v-else>
      <b-field
        label="ユーザー名"
        v-bind:type="{ 'is-success': check_name }"
        message="2～30文字"
      >
        <b-input v-model="name"></b-input>
      </b-field>

      <b-field
        label="メールアドレス"
        v-bind:type="{ 'is-success': check_email }"
        message="320文字"
      >
        <b-input type="email" v-model="email"> </b-input>
      </b-field>

      <b-field
        label="パスワード"
        v-bind:type="{ 'is-success': check_password }"
        message="8～24文字かつ半角の英小文字・英大文字・数字を1文字ずつ以上"
      >
        <b-input type="password" v-model="password" password-reveal> </b-input>
      </b-field>

      <b-field
        label="パスワード(再入力)"
        v-bind:type="{ 'is-success': check_password_confirmation }"
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
        v-bind:disabled="check"
        @click="register()"
        expanded
        >新規登録</b-button
      >
    </div>

    <b-loading
      :is-full-page="true"
      v-model="loading"
      :can-cancel="false"
    ></b-loading>
  </div>
</template>

<script>
const regex_email =
  /^(?=.{1,320}$)[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$/
const regex_password = /^(?=.*?[a-z])(?=.*?[A-Z])(?=.*?\d)[a-zA-Z\d]{8,24}$/

export default {
  name: 'Register',
  head() {
    return {
      title: '会員登録',
      meta: [{ hid: 'robots', name: 'robots', content: 'noindex' }],
    }
  },
  data() {
    return {
      name: '',
      email: '',
      password: '',
      password_confirmation: '',
      loading: false,
      error: false,
    }
  },
  mounted() {
    this.$axios
      .$get('/sanctum/csrf-cookie', {
        withCredentials: true,
      })
      .then(() => {
        this.error = false
      })
      .catch(() => {
        this.error = true
      })
  },
  computed: {
    check_name: function () {
      if (this.name.length >= 2 && this.name.length <= 30) {
        return true
      } else {
        return false
      }
    },
    check_email: function () {
      if (regex_email.test(this.email)) {
        return true
      } else {
        return false
      }
    },
    check_password: function () {
      if (regex_password.test(this.password)) {
        return true
      } else {
        return false
      }
    },
    check_password_confirmation: function () {
      if (
        this.check_password &&
        this.password &&
        this.password_confirmation &&
        this.password === this.password_confirmation
      ) {
        return true
      } else {
        return false
      }
    },
    check: function () {
      if (
        this.check_name &&
        this.check_email &&
        this.check_password &&
        this.check_password_confirmation
      ) {
        return false
      } else {
        return true
      }
    },
  },
  methods: {
    register: async function () {
      this.loading = true
      let formData = new FormData()
      formData.append('email', this.email)
      formData.append('name', this.name)
      formData.append('password', this.password)
      formData.append('password_confirmation', this.password_confirmation)

      await this.$axios
        .$post('/api/register', formData)
        .then((res) => {
          this.loading = false
          this.$auth
            .loginWith('local', {
              data: {
                email: this.email,
                name: this.name,
                password: this.password,
                password_confirmation: this.password_confirmation,
              },
            })
            .then(() => {
              location.href = '/user'
            })
            .catch(() => {
              this.$buefy.toast.open({
                message: '認証に失敗しました',
                type: 'is-danger',
                position: 'is-bottom',
              })
            })
        })
        .catch((err) => {
          this.loading = false
          this.$buefy.toast.open({
            message: '認証に失敗しました',
            type: 'is-danger',
            position: 'is-bottom',
          })
        })
    },
  },
}
</script>

<style>
.error-header {
  color: white !important;
}
</style>
