<template>
  <b-navbar>
    <template #brand>
      <b-navbar-item class="site-title" tag="nuxt-link" :to="{ path: '/' }">
        YourCount
      </b-navbar-item>
    </template>
    <template #start>
      <b-navbar-dropdown v-if="$auth.loggedIn" label="ユーザー">
        <b-navbar-item tag="nuxt-link" to="/user">情報</b-navbar-item>
        <b-navbar-item tag="nuxt-link" to="/user/setting">設定</b-navbar-item>
      </b-navbar-dropdown>
      <b-navbar-dropdown v-if="$auth.loggedIn" label="カウント">
        <b-navbar-item tag="nuxt-link" to="/count">一覧</b-navbar-item>
        <b-navbar-item tag="nuxt-link" to="/count/create">作成</b-navbar-item>
      </b-navbar-dropdown>
    </template>

    <template #end>
      <div>
        <b-navbar-item tag="div">
          <div class="buttons" v-if="$auth.loggedIn">
            <b-button type="is-danger" tag="nuxt-link" to="/logout"
              >ログアウト</b-button
            >
          </div>
          <div class="buttons" v-else>
            <b-button type="is-primary" tag="nuxt-link" to="/register"
              >会員登録</b-button
            >
            <b-button type="is-light" tag="nuxt-link" to="/login"
              >ログイン</b-button
            >
          </div>
        </b-navbar-item>
      </div>
    </template>
  </b-navbar>
</template>

<style>
.site-title {
  font-size: 24px;
}
</style>

<script>
export default {
  name: 'Header',
  data() {
    return {}
  },
  mounted() {},
}
</script>
