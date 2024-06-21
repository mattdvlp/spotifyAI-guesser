<script>
export default {
  props: ['choseArtistData', 'guessArtist'],
  data() {
    return {
      matchingKeys: [],
      guesser: {}
    }
  },
  methods: {
    isMatching(artist, key) {
      return this.guesser[key] === artist[key];
    },

    formatValue(value) {
      if (typeof value === 'string') {
        return value.charAt(0).toUpperCase() + value.slice(1);
      }
      return value;
    },

    upperLower(value) {
       if (value > this.guesser.followers) {

        return 'up';
      }
      if (value < this.guesser.followers) {
        return 'down';
      }
    }
  },

  watch: {
    choseArtistData: {
      handler: function (artist) {
        artist = artist[0];
        this.guesser = {
          name: this.guessArtist.name,
          genres: this.guessArtist.genres[0],
          followers: this.guessArtist.followers.total
        };
        let result = {};
        for (let key in this.guesser) {
          if (this.guesser.hasOwnProperty(key) && this.guesser.hasOwnProperty(key) && this.guesser[key] === artist[key]) {
            result[key] = this.guesser[key];
          }
        }
        return this.matchingKeys.push(result);
      },
      deep: true
    }
  }
}
</script>

<template>

  <div v-for="artist in choseArtistData" class="bg-gray-400 p-5 rounded border border-gray-700">
    <div v-for="(value, key) in artist">
      <div :class="{ 'text-green-200 font-bold': isMatching(artist, key) }">
        <div v-if="key === 'name' || key=== 'images'">
            <div v-if=" key === 'images' ">
              <img v-bind:src="value.url">
            </div>
            <div v-else>
              <h2 class="text-3xl font-bold">{{ formatValue(value) }}</h2>
            </div>
        </div>
        <div v-else class="grid-cols-2 grid">
          <h4>{{ formatValue(key) }}</h4>
          <p>{{ formatValue(value) }}</p> <div v-if="key === 'followers'" :class="[upperLower(value)]"></div>
        </div>
      </div>
    </div>
  </div>

</template>

<style>
img {
  width: 90px;
  height: 90px;
  object-fit: cover;
  border-radius: 50%;
}
</style>