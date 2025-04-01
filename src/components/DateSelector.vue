<template>
  <div class="date-selector">
    <button
      v-for="date in dates"
      :key="date"
      class="btn date-btn me-2"
      :class="{
        'btn-primary': date === selectedDate,
        'btn-outline-primary': date !== selectedDate,
      }"
      @click="selectDate(date)">
      {{ formatDate(date) }}
    </button>
  </div>
</template>

<script>
  import moment from 'moment';

  export default {
    name: 'DateSelector',
    props: {
      dates: {
        type: Array,
        required: true,
      },
      selectedDate: {
        type: String,
        required: true,
      },
    },
    methods: {
      formatDate(dateString) {
        return moment(dateString).format('DD MMMM');
      },
      selectDate(date) {
        this.$emit('date-selected', date);
      },
    },
  };
</script>

<style scoped>
  .date-selector {
    display: flex;
    overflow-x: auto;
    padding: 10px 0;
    margin-bottom: 20px;
  }
  .date-btn {
    min-width: 120px;
    text-align: center;
  }
</style>
