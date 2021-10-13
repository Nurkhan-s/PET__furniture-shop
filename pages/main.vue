<template>
  <div>
    <Header/>
    <nuxt-child/>
  </div>
</template>

<script>
import Header from "@/components/common/Header"

export default {
  name: "main",
  components: {
    Header
  }
}
</script>

<style scoped>

</style>
