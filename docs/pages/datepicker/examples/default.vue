<template>
  <div>
    <ev-datepicker
      v-model="bindDateNull"
      class="evui-datepicker-temp"
    />
    value: {{ bindDateNull }}
    <br>
    <ev-datepicker
      v-model="bindDay"
      :options="option"
      class="evui-datepicker-temp"
    />
    value: {{ bindDay }}
    <br>
    <ev-datepicker
      v-model="bindDay2"
      :options="option2"
      :size="'fit'"
      class="evui-datepicker-temp"
    />
    value: {{ bindDay2 }}
    <br>
    <ev-datepicker
      v-model="bindDay3"
      :options="option3"
      :size="'fit'"
      class="evui-datepicker-temp"
    />
    value: {{ bindDay3 }}
    <br>
  </div>
</template>

<script>

  export default {
    components: {
    },
    data() {
      return {
        bindDateNull: null,
        bindDay: '2018-07-09',
        option: {
          selectDayType: 'day',
          initSelectDayFlag: true,
        },
        bindDay2: '2019-04-09',
        option2: {
          selectDayType: 'day',
          initSelectDayFlag: true,
        },
        bindDay3: '2019-07-09',
        option3: {
          selectDayType: 'day',
          initSelectDayFlag: true,
          limitToday: true,
          initLimitDay: new Date(new Date().setDate(new Date().getDate() - 1)),
        },
      };
    },
    methods: {
    },
  };
</script>

<style scoped>
  .evui-datepicker-temp {
    display: inline-block;
  }
</style>
