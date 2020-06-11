<template>
  <div id="app" class="app">
    <div class="inner">
      <div class="top-line">
        <h1 class="title">TV shows</h1>
        <input
          type="text"
          class="search"
          v-model="searchRequest"
          @input="search"
        >
      </div>
      <div class="titles">
        <div
          class="info-title control"
          :class="{
            'sorted': sorting.name.sorted,
            'up': sorting.name.up
          }"
          @click="sort('name')"
        >Name</div>
        <div class="info-title">Language</div>
        <div class="info-title">Status</div>
        <div class="info-title">Genres</div>
        <div
          class="info-title control"
          :class="{
            'sorted': sorting.rating.sorted,
            'up': sorting.rating.up
          }"
          @click="sort('rating')"
        >Rating</div>
      </div>
      <div
        class="show-list"
        :class="{loading: isLoading}"
      >
        <ShowItem
          v-for="item in tvShows"
          class="item"
          :info="item"
          :key="item.show.id"
        />
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import debounce from 'debounce';
import ShowItem from './components/ShowItem.vue';

export default {
  name: 'App',
  data: () => ({
    searchRequest: 'girls',
    tvShows: [],
    isLoading: false,
    sorting: {
      name: {
        sorted: false,
        up: false,
      },
      rating: {
        sorted: false,
        up: false,
      },
    },
  }),
  components: {
    ShowItem,
  },
  beforeMount() {
    this.fetchData();
  },
  methods: {
    search: debounce(function () {
      this.fetchData();
    }, 500),
    sort(subject) {
      this.tvShows = this.tvShows.sort((a, b) => {
        let result = false;

        if (subject === 'rating') {
          if (this.sorting.rating.up) {
            result = a.score - b.score;
          } else {
            result = b.score - a.score;
          }
        }

        if (subject === 'name') {
          if (this.sorting.name.up) {
            result = a.show.name > b.show.name ? 1 : -1;
          } else {
            result = a.show.name < b.show.name ? 1 : -1;
          }

          if (a.show.name === b.show.name) {
            result = 0;
          }
        }

        return +result;
      });

      this.sorting[subject].sorted = true;
      this.sorting[subject].up = !this.sorting[subject].up;

      if (subject === 'rating') {
        this.sorting.name = {
          sorted: false,
          up: false,
        };
      } else {
        this.sorting.rating = {
          sorted: false,
          up: false,
        };
      }
    },
    fetchData() {
      this.isLoading = true;

      axios
        .get('https://api.tvmaze.com/search/shows', { params: { q: this.searchRequest } })
        .then((response) => {
          this.tvShows = response.data;

          this.sorting = {
            name: {
              sorted: false,
              up: false,
            },
            rating: {
              sorted: false,
              up: false,
            },
          };
        })
        .finally(() => {
          this.isLoading = false;
        });
    },
  },
};
</script>

<style lang="scss">
.app {
  display: flex;
  .inner {
    margin: auto;
    width: 500px;
    padding-top: 30px;
  }
  .top-line {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
  }
  .search {
    padding: 5px;
    outline: none;
    border: none;
    border-bottom: 2px solid #999;
    transition: border-bottom .2s;
    &:focus {
      border-bottom: 2px solid #000;
    }
  }
  .title {
    font: 24px/1 Verdana, Tahoma, sans-serif;
  }
  .show-list {
    transition: opacity .2s;
    &.loading {
      opacity: .2;
    }
  }
  .titles {
    display: flex;
  }
  .info-title {
    width: 20%;
    padding: 10px;
    font: bold 14px/1 Verdana, Tahoma, sans-serif;
    &.control {
      position: relative;
      cursor: pointer;
      transition: opacity .2s;
      &:hover {
        opacity: .7;
      }
    }
    &.sorted {
      &::after {
        content: '';
        position: absolute;
        right: 10px;
        top: 50%;
        width: 7px;
        height: 7px;
        border-right: 1px solid #444;
        border-bottom: 1px solid #444;
        transform: rotate(45deg) translateY(-50%);
        transition: transform .2s, right .2s;
      }
    }
    &.up {
      &::after {
        right: 6px;
        transform: rotate(-135deg);
      }
    }
  }
  .item {
    background-color: #fff;
    &:nth-child(2n) {
      background-color: #eee;
    }
  }
}
</style>
