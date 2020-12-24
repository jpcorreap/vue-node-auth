<template>
<div class="container">
  <the-login></the-login>
</div>
</template>

<script>
import TheLogin from "../components/auth/TheLogin";
export default {
  components: {
    TheLogin,
  },
};
</script>

<style>
.container {
  height: 93vh;
}
</style>