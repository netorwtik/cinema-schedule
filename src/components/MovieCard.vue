<template>
  <div class="card movie-card">
    <img :src="movie.poster" :alt="movie.title" class="card-img-top movie-poster" />
    <div class="card-body">
      <h5 class="card-title">{{ movie.title }}</h5>
      <div class="mb-2">
        <span v-if="movie.ageRating > 0" class="badge bg-warning text-dark me-1"
          >{{ movie.ageRating }}+</span
        >
        <span v-if="movie.runtime" class="badge bg-secondary me-1">{{ movie.runtime }} мин</span>
        <span v-for="genre in movie.genres" :key="genre" class="badge bg-info text-dark me-1">{{
          genre
        }}</span>
      </div>
      <div class="mt-3">
        <h6>Сеансы:</h6>
        <div class="d-flex flex-wrap">
          <ShowtimePill
            v-for="showtime in movie.showtimes"
            :key="showtime._id"
            :startTime="showtime.startTime"
            :screenName="showtime.screenName" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import ShowtimePill from './ShowtimePill.vue';

  export default {
    name: 'MovieCard',
    components: {
      ShowtimePill,
    },
    props: {
      movie: {
        type: Object,
        required: true,
      },
    },
  };
</script>

<style scoped>
  .movie-card {
    height: 100%;
    border-radius: 10px;
    overflow: hidden;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s ease;
  }
  .movie-card:hover {
    transform: translateY(-5px);
  }
  .movie-poster {
    height: 400px;
    object-fit: cover;
  }
</style>
