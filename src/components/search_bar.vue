<script>

export default {
  data() {
    return {
      artist: ''
    }
  },

  methods: {
    getProfile() {
      this.$emit('getProfile', this.artist);
      this.artist = '';
    }
  }
}
</script>

<template>
  <form @submit.prevent="getProfile()">
    <input type="text" v-model="artist" placeholder="Insert an artist">
    <input type="submit" value="Check">
  </form>
</template>

<style scoped>

</style>
