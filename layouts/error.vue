<template>
  <div>
    <h1 class="title">エラーが発生しました</h1>

    <p>しばらくしてからお試しください</p>
  </div>
</template>

<script>
export default {
  name: 'Error',
  head() {
    return {
      title: 'エラー',
      meta: [{ hid: 'robots', name: 'robots', content: 'noindex' }],
    }
  },
  data() {
    return {}
  },
}
</script>
