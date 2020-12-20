<template>
  <div>
    <NavBar />
    <div class="h-screen bg-red-100">
      <Nuxt />
    </div>
  </div>
</template>

<script>
import NavBar from "@/components/navbar.vue";
export default {
  components: {
    NavBar,
  },
};
</script>

