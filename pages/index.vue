<template>
  <div class="home">
    <UploadVideo />
  </div>
</template>

<script>
import UploadVideo from '~/components/uploadVideo'
export default {
  components: { UploadVideo }
}
</script>

<style>
.home {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100vh;
}
</style>
