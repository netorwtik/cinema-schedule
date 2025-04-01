<template>
  <div class="showtime-pill">
    <div class="fw-bold">{{ formattedTime }}</div>
    <small class="text-muted">{{ screenName }}</small>
  </div>
</template>

<script>
  import moment from 'moment';

  export default {
    name: 'ShowtimePill',
    props: {
      startTime: {
        type: String,
        required: true,
      },
      screenName: {
        type: String,
        required: true,
      },
    },
    computed: {
      formattedTime() {
        return moment(this.startTime).format('HH:mm');
      },
    },
  };
</script>

<style scoped>
  .showtime-pill {
    display: inline-block;
    padding: 8px 12px;
    margin: 5px;
    border-radius: 20px;
    background-color: #f8f9fa;
    transition: background-color 0.3s;
    cursor: pointer;
  }
  .showtime-pill:hover {
    background-color: #e9ecef;
  }
</style>
