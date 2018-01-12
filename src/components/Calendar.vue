<template>
  <div class="calendar-wrapper">
    <div class="calendar-header">
      <div class="calendar-btn-wrapper">
        <button class="left-month-btn"><</button>
        <span class="head-date">{{todays}}</span>
        <button class="right-month-btn">></button>
      </div>
    </div>
    <div class="calendar-body">
      <div class="calendar-days">
        <!-- TODO v-for -->
        <div class="calendar-week"></div>
        <!-- TODO v-for -->
        <div class="calendar-day"></div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'calendar.vue',
    props: {
      name: [String],
      inputClass: [String],
      popupClass: [String],
      value: [Date, Array, String],
      disabled: [Boolean],
      type: {
        type: String,
        default: 'normal',
      },
      rangeSeparator: {
        type: String,
        default: '~',
      },
      clearable: {
        type: Boolean,
        default: false,
      },
      placeholder: [String],
      disabledDate: {
        type: Function,
        default: () => { return false; },
      },
      format: {
        type: String,
        default: 'YYYY-MM-DD' ,
      },
      local: {
        type: Object,
        default () {
          return {
            dow: 1, // Monday is the first day of the week
            hourTip: '选择小时', // tip of select hour
            minuteTip: '选择分钟', // tip of select minute
            secondTip: '选择秒数', // tip of select second
            yearSuffix: '年', // format of head
            monthsHead: '1月_2月_3月_4月_5月_6月_7月_8月_9月_10月_11月_12月'.split('_'), // months of head
            months: '一月_二月_三月_四月_五月_六月_七月_八月_九月_十月_十一月_十二月'.split('_'), // months of panel
            weeks: '一_二_三_四_五_六_日'.split('_') // weeks
          }
        }
      }
    },
    data() {

    },
    watch() {

    },
    methods() {

    },
    mounted() {

    },
    beforeDestroy() {

    },
  };
</script>

<style scoped>

</style>
