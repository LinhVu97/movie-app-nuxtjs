<template>
  <div class="home">
    <!-- Hero -->
    <Hero />

    <!-- Search -->
    <div class="container search">
      <input
        v-model="searchInput"
        type="text"
        placeholder="Search..."
        @keyup.enter="$fetch"
      />
      <button v-show="searchInput != ''" class="button" @click="search">
        Search
      </button>
    </div>

    <Loading v-if="$fetchState.pending" />

    <!-- Movie List -->
    <div v-else class="container movies">
      <div id="movie-grid" class="movies-grid">
        <div v-for="(movie, index) in movies" :key="index" class="movie">
          <!-- Movie Image -->
          <div class="movie-img">
            <img
              :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
              alt="picture"
            />
            <p class="review">{{ movie.vote_average }}</p>
            <p class="overview">{{ movie.overview }}</p>
          </div>

          <!-- Movie Info -->
          <div class="info">
            <p class="title">
              {{ movie.title.slice(0, 25) }}
              <span v-if="movie.title.length > 25">...</span>
            </p>
            <p class="release">
              Release:
              {{
                new Date(movie.release_date).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric',
                })
              }}
            </p>
            <nuxt-link class="button button-light" :to="`/movies/${movie.id}`">
              Get More Info
            </nuxt-link>
          </div>
        </div>
      </div>
    </div>

    <!-- Pagination -->
    <b-pagination-nav
      v-model="page"
      align="center"
      :number-of-pages="total_pages"
      first-number
      last-number>
    </b-pagination-nav>
    <br>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      movies: [],
      searchInput: '',
      api_key: '909a1bf35ee993e0861aaf24a2eb25fa',
      error: null,
      page: 1,
      total_pages: '',
    }
  },
  async fetch() {
    if (this.searchInput === '') {
      await this.getMovies()
    }

    await this.search()
  },
  fetchDelay: 3000,
  head() {
    return {
      title: 'Entertainment Hub',
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: 'Get all the latest streaming movies in theaters & online',
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: 'movies, stream, stremaing',
        },
      ],
    }
  },
  methods: {
    async getMovies() {
      await axios
        .get(
          `https://api.themoviedb.org/3/movie/now_playing?api_key=${this.api_key}&language=en-US&page=${this.page}`
        )
        .then((res) => {
          this.movies = res.data.results
          this.total_pages = res.data.total_pages
        })
        .catch((err) => (this.error = err))
    },
    async search() {
      await axios
        .get(
          `https://api.themoviedb.org/3/search/movie?api_key=${this.api_key}&language=en-US&page=1&query=${this.searchInput}`
        )
        .then((res) => {
          this.movies = res.data.results
        })
        .catch((err) => (this.error = err))
      this.searchInput = ''
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
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
    }
  }
  .movies {
    padding: 32px 16px;
    .movies-grid {
      display: grid;
      column-gap: 32px;
      row-gap: 64px;
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
      .movie {
        position: relative;
        display: flex;
        flex-direction: column;
        .movie-img {
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
          .review {
            position: absolute;
            top: 0;
            left: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 40px;
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
          .title {
            margin-top: 8px;
            color: #fff;
            font-size: 20px;
          }
          .release {
            margin-top: 8px;
            color: #c9c9c9;
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
