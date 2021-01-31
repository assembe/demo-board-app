<template>
  <div>
    <cards-example></cards-example>
  </div>
</template>

<script>
import Board from "./assembe/examples/Board";

export default {
  name: 'Dashboard',
  components: {CardsExample: Board},
  computed: {
    user() {
      return this.$store.getters.user;
    }
  }
}
</script>

<style scoped>
h1, h2{
  font-weight: normal;
  text-align: center;
}

</style>
