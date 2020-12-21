<template>
  <!-- responsive embedded Giphy gif (generated from the Giphy website) -->
  <div style="width: 100%; height: 0; padding-bottom: 66%; position: relative">
    <iframe
      :src="url"
      width="100%"
      height="100%"
      style="position: absolute"
      frameBorder="0"
      class="giphy-embed"
      allowFullScreen
    ></iframe>
  </div>
</template>

<script>
export default {
  props: ["url"],
};
</script>
