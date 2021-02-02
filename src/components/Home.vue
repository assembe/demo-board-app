<template>
  <div>
    <div class="text-center">
      <img src="../assets/logo.svg" alt="Assembe Logo" class="logo">
    </div>
    <h1 class="title text-h2 mt-4">Assembe Board demo</h1>
    <p class="mt-2 text--secondary text-center text-subtitle-1">This is demo application showing functionality of Assembe.</p>
    <hr class="mt-10 mb-10">
    <p class="mt-2 text-center text-subtitle-1">
      If you want to use it in your project, remember to correct your API URL in <code>config/assembe.js</code> in <code>apiHost</code> property. <br>
      If you need an account, please register on <a href="https://assembe.com/register" class="d-inline">assembe.com/register</a>.
    </p>
  </div>
</template>

<script>
  export default {
    name: 'Home',
  }
</script>

<style scoped>
  h1, h2 {
    font-weight: normal;
    text-align: center;
  }

  .logo {
    width: 400px;
    max-width: 90%;
  }

</style>
