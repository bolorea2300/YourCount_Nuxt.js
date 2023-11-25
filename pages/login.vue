<template>
  <div>
    <h1 class="title">ログイン</h1>

    <div v-if="error">
      <h2 class="subtitle">エラーが発生しました</h2>
    </div>

    <div v-else>
      <b-field
        label="メールアドレス"
        v-bind:type="{ 'is-success': check_email }"
        v-bind:message="{ このメールアドレスは正しい形式です: check_email }"
      >
        <b-input type="email" v-model="email"> </b-input>
      </b-field>

      <b-field
        label="パスワード"
        v-bind:type="{ 'is-success': check_password }"
        v-bind:message="{ このパスワードは正しい形式です: check_password }"
      >
        <b-input type="password" v-model="password" password-reveal> </b-input>
      </b-field>

      <b-button
        type="is-primary"
        v-bind:disabled="check"
        @click="login()"
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
  name: 'Login',
  head() {
    return {
      title: 'ログイン',
      meta: [{ hid: 'robots', name: 'robots', content: 'noindex' }],
    }
  },
  data() {
    return {
      email: '',
      password: '',
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
    check: function () {
      if (this.check_email && this.check_password) {
        return false
      } else {
        return true
      }
    },
  },
  methods: {
    login: async function () {
      this.loading = true
      await this.$auth
        .loginWith('local', {
          data: {
            email: this.email,
            password: this.password,
          },
        })
        .then(() => {
          this.loading = false
          location.href = '/user'
        })
        .catch(() => {
          this.loading = false
          this.$buefy.toast.open({
            message: 'ログインに失敗しました',
            type: 'is-danger',
            position: 'is-bottom',
          })
        })
    },
  },
}
</script>
