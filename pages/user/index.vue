<template>
  <div>
    <h1 class="title">ユーザー</h1>

    <div class="buttons">
      <b-button type="is-primary" tag="nuxt-link" to="/count" expanded
        >カウント一覧</b-button
      >
      <b-button type="is-success" tag="nuxt-link" to="/count/create" expanded
        >カウント作成</b-button
      >
      <b-button type="is-info" tag="nuxt-link" to="/user/setting" expanded
        >ユーザー設定</b-button
      >
    </div>
  </div>
</template>

<script>
export default {
  name: 'User',
  head() {
    return {
      title: 'ユーザー',
      meta: [{ hid: 'robots', name: 'robots', content: 'noindex' }],
    }
  },
}
</script>
