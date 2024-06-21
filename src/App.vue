<script>
import search_bar from "@/components/search_bar.vue";
import result from '@/components/result.vue'

export default {
  components: {search_bar, result},
  data() {
    return {
      accessToken: '',
      choseArtistData: [],
      selectedArtist: '',
      guessArtist: '',
      possiblePlaylist: ['37i9dQZEVXbMDoHDwVN2tF','37i9dQZEVXbIQnj7RRhdSX', '5OAP85i7k0OiAGgt80O6y1', '3IoldLG5Nyf7p3kl2pBxRC']
    }
  },

  async mounted () {
    let date = Date.now();
    let storage = localStorage.getItem('spotify_token');
    if(storage !== null && JSON.parse(storage).expiresOn < date){
      localStorage.removeItem('spotify_token');
    }
    if(localStorage.getItem('spotify_token') == null){
      localStorage.setItem('spotify_token', JSON.stringify({
        value: await this.getAccess(),
        expiresOn: date + 1000*60
      }));
    }
    this.accessToken = JSON.parse(localStorage.getItem('spotify_token')).value;

  },

  methods: {
    getGuessArtist() {
        let playlist = this.possiblePlaylist[Math.floor(Math.random() * this.possiblePlaylist.length)];
        this.getSpotifyCall('', playlist);
    },

    getAccess() {
      const myHeaders = new Headers();
      myHeaders.append("Content-Type", "application/x-www-form-urlencoded");

      const requestOptions = {
        method: "POST",
        headers: myHeaders,
      };

      const response = fetch("https://accounts.spotify.com/api/token?grant_type=client_credentials&client_id=c1287bb88f934bb7a8efa257a72d4b21&client_secret=9060ad2db3c74509b1e83a335cb5c23a", requestOptions)
          .then(response => response.json())
          .then(function (result) {return result.access_token})
      return response;
    },

    async getSpotifyCall(artist, playlist = null) {
      let call;
      let artistID;
      if (playlist !== null) {
        call = 'https://api.spotify.com/v1/playlists/' + playlist + '/tracks?fields=items(track(artists(id)))'
      } else {
        call = 'https://api.spotify.com/v1/search?q=' + encodeURI(artist) + '&type=artist'
      }
      const response = await fetch(call, {
        headers: {
          Authorization: 'Bearer ' + this.accessToken
        }
      });

      const data = await response.json();
      if (playlist !== null) {
        artistID = data.items[Math.floor(Math.random() * data.items.length)].track.artists[0].id;
        if (artistID === this.guessArtist.id) {
          this.getGuessArtist();
        }
        this.guessArtist = await this.getSpotifyCallID(artistID);
        } else {
        let artist = data.artists.items[0];
        console.log(data.artists);
        this.choseArtistData.unshift({name: artist.name, images: artist.images[0], genres: artist.genres[0], followers: artist.followers.total});
      }
    },

    async getSpotifyCallID(artistID) {
      const response = await fetch('https://api.spotify.com/v1/artists/' + encodeURI(artistID), {
        headers: {
          Authorization: 'Bearer ' + this.accessToken
        }
      });
        return await response.json();
    }
  }
};

</script>
<template>
<div @click="getGuessArtist">Create new guesser</div>
  <search_bar @getProfile="getSpotifyCall"></search_bar>

  <result :choseArtistData="choseArtistData" :guessArtist="guessArtist"></result>
</template>
<style>

</style>
