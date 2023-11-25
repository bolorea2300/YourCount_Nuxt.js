<template>
  <div>
    <div class="container">
      <Header></Header>

      <section class="main-content">
        <div class="container">
          <Nuxt />
        </div>
      </section>
    </div>
  </div>
</template>

<script>
import Header from '@/components/Header.vue'

export default {
  name: 'Layout',
  components: {
    Header,
  },
  data() {
    return {}
  },
}
</script>
