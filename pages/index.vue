<template>
  <div class="home">
    <Hero />


    <!-- Search Bar -->
    <div class="container search">
      <input v-model.lazy="searchInput" type="text" placeholder="Search"  />
      <button class="button" @click="$fetch" >
        Search
      </button>
    </div>
        
    <Loading v-if="$fetchState.pending" />

    <!-- Album Grid  -->
    <div v-else class="container albums">
      <div id="album-grid" class="albums-grid">
        <div  v-for="(album,index) in albums" :key="index" class="album">
          <div class="album-img">
            <img :src="album.artworkUrl100.slice(0, album.artworkUrl100.length-13).concat('600x600bb.jpg')" alt = "">
            <p class="price">${{ album.collectionPrice }}</p>
            <p class="overview">{{ album.trackCount }} track(s)</p>
          </div>
          <div class="info">
            <p class="title ">
              {{ album.collectionName.slice(0, 25)
              }}<span v-if="album.collectionName.length > 25">...</span>
            </p>
            <p class="release">
              Released:
              {{
                new Date(album.releaseDate).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </p>
            <NuxtLink
              class="button button-light "
              :to="{ name: 'albums-albumid', params: { id: album.collectionId, release: album.releaseDate, price: album.collectionPrice, track_count: album.trackCount-1, link: album.collectionViewUrl, genre: album.primaryGenreName, artwork: album.artworkUrl100.slice(0, album.artworkUrl100.length-13).concat('600x600bb.jpg')} }"
            >
              More Info
            </NuxtLink>
          </div>
        </div>
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
import Hero from '../components/Hero.vue'

export default {
  name: 'IndexPage',
  components: { Hero },
  
  data(){
    return{
      albums: [],
      searchInput: '',
    }
  },

  async fetch() {
    if (this.searchInput === '') {
      await this.getAlbums()
    }
    if (this.searchInput !== '') {
      await this.searchAlbums()
    }
  },
  fetchDelay: 1000,

  methods: {
    async getAlbums(){
      const data = axios.get(`https://itunes.apple.com/search?term=jovi&limit=25&entity=album`)
      const result = await data
      // console.log(result.data.results)
      this.albums.splice(0)
      result.data.results.forEach((album) => {
      this.albums.push(album)
      })
    },
    async searchAlbums() {
      const data = axios.get(
        `https://itunes.apple.com/search?term=${this.searchInput}&limit=30&entity=album`
      )
      const result = await data
      this.albums.splice(0)
      result.data.results.forEach((album) => {
      this.albums.push(album)
      })
    },
  },
}
</script>

<style lang="scss">
.home {
  .loading {
    padding-top: 120px;
    align-items: flex-start;
  }
  .search {
    display: flex;
    padding: 32px 16px;
    input {
      max-width: 350px;
      width: 100%;
      padding: 12px 6px;
      font-size: 14px;
      border: none;
      &:focus {
        outline: none;
      }
          border-style:solid;
        border-width: thin;

    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
    }
  }
  .albums {
    padding: 32px 16px;
    .albums-grid {
      display: grid;
      column-gap: 32px;
      row-gap: 44px;
      grid-template-columns: 1fr;
      @media (min-width: 500px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 750px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 1100px) {
        grid-template-columns: repeat(4, 1fr);
      }
      .album {
        position: relative;
        display: flex;
        flex-direction: column;
        border-style: solid;
        border-width: thin;
        padding: 8px;
        .album-img {
          position: relative;
          overflow: hidden;
          &:hover {
            .overview {
              transform: translateY(0);
            }
          }
          img {
            display: block;
            width: 100%;
            height: 100%;
          }
          .price {
            position: absolute;
            top: 0;
            left: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 60px;
            height: 40px;
            background-color: #c92502;
            color: #fff;
            border-radius: 0 0 16px 0;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
              0 2px 4px -1px rgba(0, 0, 0, 0.06);
          }
          .overview {
            line-height: 1.5;
            position: absolute;
            bottom: 0;
            background-color: rgba(201, 38, 2, 0.9);
            padding: 12px;
            color: #fff;
            transform: translateY(100%);
            transition: 0.3s ease-in-out all;
          }
        }
        .info {
          margin-top: auto;
          background-color: #fff;
          .title {
            margin-top: 8px;
            color: #000;
            font-size: 20px;
          }
          .release {
            margin-top: 8px;
            color: #707070;
          }
          .button {
            margin-top: 8px;
          }
          
        }
      }
    }
  }
}
</style>