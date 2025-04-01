<template>
  <div class="filter-section row mb-4">
    <div class="col-md-4">
      <div class="input-group">
        <label class="input-group-text" for="sortingSelect">Сортировка:</label>
        <select class="form-select" id="sortingSelect" v-model="sortValue" @change="emitSortChange">
          <option value="title">По названию</option>
          <option value="showtimesCount">По количеству сеансов</option>
          <option value="nearestShowtime">По ближайшему сеансу</option>
        </select>
      </div>
    </div>
    <div class="col-md-8">
      <div class="d-flex flex-wrap">
        <div class="me-3 mb-2">
          <label class="me-2">Зал:</label>
          <div class="btn-group" role="group">
            <button
              type="button"
              class="btn btn-sm"
              :class="screenValue === 0 ? 'btn-secondary' : 'btn-outline-secondary'"
              @click="setScreenFilter(0)">
              Все
            </button>
            <button
              v-for="screen in screens"
              :key="screen"
              type="button"
              class="btn btn-sm"
              :class="screenValue === screen ? 'btn-secondary' : 'btn-outline-secondary'"
              @click="setScreenFilter(screen)">
              {{ screen }}
            </button>
          </div>
        </div>
        <div>
          <label class="me-2">Жанр:</label>
          <div class="btn-group" role="group">
            <button
              type="button"
              class="btn btn-sm"
              :class="genreValue === '' ? 'btn-secondary' : 'btn-outline-secondary'"
              @click="setGenreFilter('')">
              Все
            </button>
            <button
              v-for="genre in genres"
              :key="genre"
              type="button"
              class="btn btn-sm"
              :class="genreValue === genre ? 'btn-secondary' : 'btn-outline-secondary'"
              @click="setGenreFilter(genre)">
              {{ genre }}
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'FilterPanel',
    props: {
      screens: {
        type: Array,
        required: true,
      },
      genres: {
        type: Array,
        required: true,
      },
      sortOption: {
        type: String,
        default: 'title',
      },
      screenFilter: {
        type: Number,
        default: 0,
      },
      genreFilter: {
        type: String,
        default: '',
      },
    },
    data() {
      return {
        sortValue: this.sortOption,
        screenValue: this.screenFilter,
        genreValue: this.genreFilter,
      };
    },
    methods: {
      emitSortChange() {
        this.$emit('sort-changed', this.sortValue);
      },
      setScreenFilter(screen) {
        this.screenValue = screen;
        this.$emit('screen-changed', screen);
      },
      setGenreFilter(genre) {
        this.genreValue = genre;
        this.$emit('genre-changed', genre);
      },
    },
  };
</script>

<style scoped>
  .filter-section {
    margin-bottom: 20px;
    padding: 15px;
    background-color: #f8f9fa;
    border-radius: 8px;
  }
</style>
