<template>
  <div class="container py-4">
    <h1 class="mb-4 text-center">Расписание кинотеатра</h1>

    <DateSelector
      :dates="availableDates"
      :selectedDate="selectedDate"
      @date-selected="changeDate" />

    <FilterPanel
      :screens="uniqueScreens"
      :genres="uniqueGenres"
      :sortOption="sortOption"
      :screenFilter="screenFilter"
      :genreFilter="genreFilter"
      @sort-changed="sortOption = $event"
      @screen-changed="screenFilter = $event"
      @genre-changed="genreFilter = $event" />

    <div v-if="moviesForSelectedDate.length > 0">
      <div class="row">
        <div class="col-md-6 col-lg-4 mb-4" v-for="movie in moviesForSelectedDate" :key="movie._id">
          <MovieCard :movie="movie" />
        </div>
      </div>
    </div>
    <div v-else class="alert alert-info">Нет сеансов на выбранную дату.</div>
  </div>
</template>

<script>
  import moment from 'moment';
  import 'moment/locale/ru';
  import { moviesData } from './data/movies';
  import { datesData } from './data/dates';
  import DateSelector from './components/DateSelector.vue';
  import FilterPanel from './components/FilterPanel.vue';
  import MovieCard from './components/MovieCard.vue';

  export default {
    name: 'App',
    components: {
      DateSelector,
      FilterPanel,
      MovieCard,
    },
    data() {
      return {
        allMovies: moviesData,
        availableDates: datesData,
        selectedDate: datesData[0],
        sortOption: 'title',
        screenFilter: 0,
        genreFilter: '',
      };
    },
    computed: {
      uniqueScreens() {
        const screens = new Set();
        this.allMovies.forEach((movie) => {
          movie.showtimes.forEach((showtime) => {
            screens.add(showtime.screenId);
          });
        });
        return Array.from(screens).sort((a, b) => a - b);
      },
      uniqueGenres() {
        const genres = new Set();
        this.allMovies.forEach((movie) => {
          if (movie.genres) {
            movie.genres.forEach((genre) => {
              genres.add(genre);
            });
          }
        });
        return Array.from(genres).sort();
      },
      moviesForSelectedDate() {
        let result = this.allMovies.filter((movie) => {
          const hasShowtimesForSelectedDate = movie.showtimes.some((showtime) => {
            const showtimeDate = this.formatDate(showtime.startTime);
            return showtimeDate === this.selectedDate;
          });
          return hasShowtimesForSelectedDate;
        });

        result = result.map((movie) => {
          const filteredMovie = { ...movie };
          filteredMovie.showtimes = movie.showtimes.filter((showtime) => {
            const showtimeDate = this.formatDate(showtime.startTime);
            return showtimeDate === this.selectedDate;
          });
          return filteredMovie;
        });

        if (this.screenFilter !== 0) {
          result = result.filter((movie) => {
            const hasScreenShowtimes = movie.showtimes.some(
              (showtime) => showtime.screenId === this.screenFilter,
            );
            return hasScreenShowtimes;
          });

          result = result.map((movie) => {
            const filteredMovie = { ...movie };
            filteredMovie.showtimes = movie.showtimes.filter(
              (showtime) => showtime.screenId === this.screenFilter,
            );
            return filteredMovie;
          });
        }

        if (this.genreFilter !== '') {
          result = result.filter(
            (movie) => movie.genres && movie.genres.includes(this.genreFilter),
          );
        }
        if (this.sortOption === 'title') {
          result.sort((a, b) => a.title.localeCompare(b.title));
        } else if (this.sortOption === 'showtimesCount') {
          result.sort((a, b) => b.showtimes.length - a.showtimes.length);
        } else if (this.sortOption === 'nearestShowtime') {
          result.sort((a, b) => {
            const aEarliestTime = Math.min(
              ...a.showtimes.map((s) => new Date(s.startTime).getTime()),
            );
            const bEarliestTime = Math.min(
              ...b.showtimes.map((s) => new Date(s.startTime).getTime()),
            );
            return aEarliestTime - bEarliestTime;
          });
        }

        return result;
      },
    },
    methods: {
      formatDate(dateString) {
        return moment(dateString).format('YYYY-MM-DD');
      },
      changeDate(date) {
        this.selectedDate = date;
        // Обновляем URL страницы
        const url = new URL(window.location);
        url.searchParams.set('date', date);
        window.history.pushState({}, '', url);
      },
    },
    mounted() {
      moment.locale('ru');

      const urlParams = new URLSearchParams(window.location.search);
      const dateParam = urlParams.get('date');

      if (dateParam && this.availableDates.includes(dateParam)) {
        this.selectedDate = dateParam;
      }
    },
  };
</script>

<style></style>
