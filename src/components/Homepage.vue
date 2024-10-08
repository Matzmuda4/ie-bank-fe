<template>
  <div class="home-container">
    <div class="jumbotron">
      <h1 class="display-4">Welcome to IE Bank!</h1>
      <p class="lead">Manage your bank accounts easily and efficiently.</p>
      <hr class="my-4" />
      <p>Click the button below to access your accounts and manage them.</p>
      <router-link to="/accounts" class="btn btn-primary btn-lg">Go to Accounts</router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: "Homepage"
};
</script>

<style scoped>
.home-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f8f9fa;
}

.jumbotron {
  text-align: center;
  background-color: white;
  padding: 2rem;
  border-radius: 1rem;
}

.btn-lg {
  margin-top: 20px;
}
</style>
