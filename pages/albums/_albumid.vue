<template>
  <Loading v-if="$fetchState.pending" />

  <!-- Album Info -->
  <div v-else class="single-album container ">
    <div> <NuxtLink class="button" style="display: inline-block; vertical-align: middle; margin: -10px 30px;" :to="{ name: 'index' }"> Back </NuxtLink> 
    <h1 style="text-align:center; vertical-align: middle;"> Album Information</h1>
  </div>
  <br>
  <div class = "border-bottom box-shadow">
  </div>
  <br>
  <br>
  <div class="album-info">
    <div class="album-img">
      <img class="icon" :src="$route.params.artwork" alt = "">
        <br>
        <p class="album-fact">
          <span>Released:</span>
          {{
            new Date($route.params.release).toLocaleString('en-us', {
              month: 'long',
              day: 'numeric',
              year: 'numeric',
            })
          }}
        </p>
        <p class="album-fact">
          <span>Genre:</span> {{ $route.params.genre }} 
        </p>
        <p class="album-fact">
          <span>Price:</span>
          {{
            $route.params.price.toLocaleString('en-us', {
              style: 'currency',
              currency: 'USD',
            })
          }}
        </p>
      </div>
      <div class="album-content">
        <h1>{{album_info[0].collectionName }}</h1>
        <p class="album-fact">
          <span>Tracklist:</span>
        </p>
        <div  v-for="(track,index) in album_info.slice(1,album_info.length)" :key="index">
          <p >{{ index+1 }}. {{ track.trackName }}</p>
        </div>
        <br>
        <a class="button" :href="$route.params.link"> Listen </a>
      </div>
      </div>
          <footer class="border-top footer text-muted">
          <div style="margin-left: 90px">
              &copy; 2022 - R. N.
          </div>
      </footer>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'singleAlbum',
  async fetch() {
    await this.getSingleAlbum()
  },
  // delay for fetching
  fetchDelay: 1000,
  head() {
    return {
      title: this.album_info.collectionName,
    }
  },
  data() {
    return {
      album_info: [],
    }
  },
  methods: {
    async getSingleAlbum() {
      const data = axios.get(
        `https://itunes.apple.com/lookup?id=${this.$route.params.id}&entity=song`
      )
      const result = await data
      this.album_info.splice(0)
      // console.log(result.data.results)
      result.data.results.forEach((album) => {
      this.album_info.push(album)
      // console.log(this.album_info[0].collectionName)

      })
    },
  },
}
</script>

<style lang="scss">
.icon{
  height: 450px;
  width: 450px;
}
.single-album {
  color: #000;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 32px 16px;
  .button {
    align-self: flex-start;
    margin-bottom: 32px;
  }
    .button2 {
    align-self: flex-start;
    margin-bottom: 32px;
  }
  .album-info {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 32px;
    color: #000;
    @media (min-width: 800px) {
      flex-direction: row;
      align-items: flex-start;
    }
    .album-img {
      img {
        max-height: 500px;
        width: 100%;
        @media (min-width: 800px) {
          max-height: 700px;
          width: initial;
        }
      }
    }
    .album-content {
      h1 {
        font-size: 56px;
        font-weight: 400;
        display: inline
      }
      .album-fact {
        margin-top: 12px;
        font-size: 20px;
        line-height: 1.5;
        span {
          font-weight: 600;
          text-decoration: underline;
        }
      }
      .tagline {
        font-style: italic;
        span {
          font-style: normal;
        }
      }
    }
  }
}
</style>