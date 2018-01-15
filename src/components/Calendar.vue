<template>
  <div class="calendar-wrapper">
    <div class="calendar-header">
      <div class="calendar-btn-wrapper">
        <button class="left-month-btn" @click="goToPrevMonth"><</button>
        <span class="head-date">{{today}}</span>
        <button class="right-month-btn" @click="goToNextMonth">></button>
      </div>
    </div>
    <div class="calendar-body">
      <div class="calendar-days">
        <div class="calendar-week">
          <a v-for="week in options.weeks">{{week}}</a>
        </div>
        <div class="calendar-day">
          <div v-for="(weekList, index) in dateItemCollection.getItemList()" v-if="dateItemCollection.isWeekVisible(index)">
            <a v-for="dateItem in weekList" @click="selectDate(dateItem)" :class="{selected: dateItem.selected}">
              {{dateItem.viewDate}}
            </a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  /* eslint-disable no-console */
  import moment from 'moment';


  window.moment = moment;
  class DateItemCollection {
    constructor(vm) {
      this.itemList = [];
      this.index = 0;
      this.pivotDate = moment().startOf('month');
      this.weekVisibleList = [];
      this.vm = vm;
    }

    setItem(item) {
      this.itemList.push(item);
    }

    getItemList() {
      return this.itemList;
    }

    clear() {
      this.itemList = [];
    }

    createItemInDuration(center = moment()) {
      const start = moment(center).add(-6, 'month').startOf('month');
      const end = moment(center).add(6, 'month').endOf('month');

      const diff = end.diff(start, 'day');
      let i = 0;
      const _itemList = [];
      const spliceItemList = [];
      const testList = [];

      for (i = 0; i < diff; i += 1) {
        testList.push(moment(start).add(i , 'day').format('YYYY-MM-DD'));
        _itemList.push(new DateItem(moment(start).add(i , 'day')));
      }

      const weekday = start.day();
      for (i = weekday; i >= 1; i -= 1) {
        _itemList.unshift(new DateItem(moment(start).add(-i, 'day')));
      }


      const endWeekday = end.day();
      for (i = 1; i <= 6 - endWeekday; i += 1) {
        _itemList.push(new DateItem(moment(end).add(i, 'day')));
      }


      const diffWeek = diff / 7;

      for (i = 0; i < diffWeek; i += 1) {
        spliceItemList.push(_itemList.splice(0, 7));
      }

      console.log(testList);

      this.itemList = spliceItemList;
      this.updateWeekVisible();
    }

    updateWeekVisible() {
      const weekCount = this.itemList.length;
      let i = 0;
      const pivotToMoment = moment(this.pivotDate);
      const pivotStartDate = pivotToMoment.startOf('month');
      const pivotEndDate = moment(this.pivotDate).add(this.range, 'month').endOf('month')
      for (i = 0; i < weekCount; i += 1) {
        const startDayVisible = this.itemList[i][0].date.isBetween(pivotStartDate, pivotEndDate);
        const endDayVisible = this.itemList[i][this.itemList[i].length - 1].date.isBetween(pivotStartDate, pivotEndDate);
        this.weekVisibleList[i] = (startDayVisible || endDayVisible);
      }
      this.vm.$forceUpdate();
    }

    isWeekVisible(weekIndex) {
      return this.weekVisibleList[weekIndex];
    }

    setPivotDate(pivotDate) {
      this.pivotDate = moment(pivotDate).startOf('month');
    }

    setCalendarRange(range) {
      this.range = range - 1;
    }
  }

  class DateItem {
    constructor(date, state) {
      this.date = date;
      this.status = state || 'NORMAL';
      this.disabled = false;
      this.selected = false;
      this.isRange = false;
    }

    get state() {
      return this.status;
    }
    set state(_status) {
      this.status = _status;
    }
    get viewDate() {
      return this.date.format('DD');
    }
  }

  export default {
    name: 'Calendar',
    data() {
      return {
        today: moment().format('YYYY-MM-DD'),
        dateList: {
          prev: [],
          next: [],
          to: [],
        },
        dateItemCollection: new DateItemCollection(this),
        options: {
          format: 'YYYY-MM-DD',
          weeks: ['일', '월', '화', '수', '목', '금', '토'],
        },
      };
    },
    watch: {
      today() {
        this.drawCalendar();
      },
    },
    methods: {
      createDateListItem() {

      },
      drawCalendar() {
        const lastDate = this.getLastDate();
        const startDate = this.getStartDate();
        const prevDates = this.getPrevDate(this.getStartDate());
        const nextDates = this.getNextDate(this.getLastDate());
        const dayArray = [];
        let i = 0;
        const startDateToInt = parseInt(startDate.format('DD'), 10);
        const lastDateToInt = parseInt(lastDate.format('DD'), 10);

        for (i = startDateToInt - 1; i < lastDateToInt; i += 1) {
          console.log(startDate.format('YYYYMMDD'));
          dayArray.push(new DateItem(moment(startDate).add(i, 'day')));
        }

        this.dateList.prev = prevDates;
        this.dateList.next = nextDates;
        const result = [].concat(prevDates, dayArray, nextDates);
      },
      cleanCalendar() {
        this.dateList.prev = [];
        this.dateList.next = [];
        this.dateList.to = [];
      },
      getPrevDate(startDate) {
        const weekday = startDate.day();
        const weekdays = [];
        let i = 0;
        for (i = weekday; i >= 1; i -= 1) {
          weekdays.push(new DateItem(moment(startDate).add(-i, 'day')));
        }
        return weekdays;
      },
      getNextDate(lastDate) {
        const weekday = lastDate.day();
        const weekdays = [];
        let i = 0;
        for (i = 1; i <= 6 - weekday; i += 1) {
          weekdays.push(new DateItem(moment(lastDate).add(i, 'day')));
        }
        return weekdays;
      },

      getLastDate() {
        return this.getToday().endOf('month');
      },
      getStartDate() {
        return this.getToday().startOf('month');
      },
      getWeekName() {
        return this.getToday().day();
      },
      getToday() {
        return moment(this.today, this.options.format);
      },
      goToNextMonth() {
        this.today = this.getToday().add(1, 'month').format(this.options.format);
        console.log(this.today);
        this.dateItemCollection.setPivotDate(this.today);
        this.dateItemCollection.updateWeekVisible();
      },
      goToPrevMonth() {
        this.today = this.getToday().add(-1, 'month').format(this.options.format);
        console.log(this.today);
        this.dateItemCollection.setPivotDate(this.today);
        this.dateItemCollection.updateWeekVisible();
      },
      selectDate(dateItem) {
        dateItem.selected = !dateItem.selected;
      },
    },
    mounted() {
      this.drawCalendar();
      this.dateItemCollection.createItemInDuration();
      this.dateItemCollection.setCalendarRange(1);
      this.dateItemCollection.setPivotDate(moment().startOf('month'));
    },
    beforeDestroy() {

    },
  };
</script>

<style scoped>
  .prev-date {
    color: #777
  }

  a.selected {
    color: #ff0000;
  }
  a {
    cursor: pointer;
  }
</style>
