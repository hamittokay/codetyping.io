<template>
  <div class="w-full min-h-screen bg-gray-800">
    <Navbar />
    <Nuxt />
  </div>
</template>

<script>
import Vue from "vue";
import navbar from "~/components/navbar.vue";

export default Vue.extend({
  name: "navbar",
  components: { navbar }
});
</script>
