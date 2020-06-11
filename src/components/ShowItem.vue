<template>
  <div class="show-item">
    <div class="info-item">{{ info.show.name }}</div>
    <div class="info-item">{{ info.show.language }}</div>
    <div class="info-item">{{ info.show.status }}</div>
    <div class="info-item">
      <div
        class="genre"
        v-for="(genre, index) in info.show.genres"
        :key="index"
      >
        {{ genre }}
      </div>
    </div>
    <div class="info-item">{{ info.score|round }}</div>
  </div>
</template>

<script>
export default {
  name: 'ShowItem',
  props: {
    info: Object,
  },
  filters: {
    round(value) {
      return Math.round(value * 10) / 10;
    },
  },
};
</script>

<style lang="scss">
.show-item {
  display: flex;

  .info-item {
    width: 20%;
    padding: 10px;
  }
}
</style>
