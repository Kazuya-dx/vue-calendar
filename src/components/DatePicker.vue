<template>
  <div>
    <p>カレンダー</p>
    <button @click="goLastMonth()">前の週</button>
    <button @click="goNextMonth()">次の週</button>

    <!-- TODO: key の設定方法を見直す -->
    <h2>{{ getYearMonth(getLastMonth(currentDate)) }}</h2>
    <div
      v-for="(week, index) in calendars.lastMonth"
      :key="week[0] + index"
      style="display: flex"
    >
      <div v-for="date in week" :key="date" style="margin: 0 8px">
        {{ getDay(date) }}
      </div>
    </div>
    <hr />

    <h2>{{ getYearMonth(currentDate) }}</h2>
    <div
      v-for="(week, index) in calendars.thisMonth"
      :key="week[0] + index"
      style="display: flex"
    >
      <div v-for="date in week" :key="date" style="margin: 0 8px">
        {{ getDay(date) }}
      </div>
    </div>
    <hr />

    <h2>{{ getYearMonth(getNextMonth(currentDate)) }}</h2>
    <div
      v-for="(week, index) in calendars.nextMonth"
      :key="week[0] + index"
      style="display: flex"
    >
      <div v-for="date in week" :key="date" style="margin: 0 8px">
        {{ getDay(date) }}
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import moment from "moment";

export default Vue.extend({
  data() {
    return {
      currentDate: moment().format("YYYY-MM-DD"),
    };
  },

  computed: {
    calendars() {
      return {
        lastMonth: (this as any).getCalendar(
          (this as any).getLastMonth(this.currentDate)
        ),
        thisMonth: (this as any).getCalendar(this.currentDate),
        nextMonth: (this as any).getCalendar(
          (this as any).getNextMonth(this.currentDate)
        ),
      };
    },
  },

  methods: {
    goNextMonth() {
      this.currentDate = moment(this.currentDate)
        .add(1, "month")
        .format("YYYY-MM-DD");
    },
    goLastMonth() {
      this.currentDate = moment(this.currentDate)
        .add(-1, "month")
        .format("YYYY-MM-DD");
    },
    getLastMonth(targetDate: string) {
      return moment(targetDate).add(-1, "month").format("YYYY-MM-DD");
    },

    getNextMonth(targetDate: string) {
      return moment(targetDate).add(1, "month").format("YYYY-MM-DD");
    },

    getYearMonth(targetDate: string) {
      return moment(targetDate).format("YYYY-MM");
    },

    getDay(targetDate: string) {
      return moment(targetDate).format("DD");
    },

    /**
     * 対象日付が含まれるカレンダーの最初の日付(月曜日)を返す。
     * @param targetDate 対象日付
     */
    getStartDate(targetDate: string) {
      let date = moment(targetDate);
      // その月の最初の日を取得
      date.startOf("month");
      // 曜日を算出(日曜日:0, 月曜日:1, ...)
      const dayOfWeekNum = date.day();
      // カレンダーの最初の日付を返す(月の最初の日から引き算より月曜日を算出)
      return date.subtract(dayOfWeekNum - 1, "days");
    },

    /**
     * 対象日付が含まれるカレンダーの最後の日付(日曜日)を返す。
     * @param targetDate 対象日付
     */
    getEndDate(targetDate: string) {
      let date = moment(targetDate);
      // その月の最後の日を取得
      date.endOf("month");
      // 曜日を算出(日曜日:0, 月曜日:1, ...)
      const dayOfWeekNum = date.day();
      // カレンダーの最後の日付を返す(月の最後の日から足し算より日曜日を算出)
      return date.add(7 - dayOfWeekNum, "days");
    },

    /**
     * 対象日付が含まれるカレンダーを返す
     * @param targetDate 対象日付
     */
    getCalendar(targetDate: string) {
      let startDate = this.getStartDate(targetDate);
      let endDate = this.getEndDate(targetDate);
      // CHECK: カレンダーの週の数は固定するか
      let weekNum = 6;

      let calendars: string[][] = [];
      for (let week = 0; week < weekNum; week++) {
        let weekRow: string[] = [];
        for (let day = 0; day < 7; day++) {
          weekRow = [
            ...weekRow,
            startDate
              .clone()
              .add(day + week * 7, "days")
              .format("YYYY-MM-DD"),
          ];
        }
        calendars = [...calendars, weekRow];
      }

      return calendars;
    },
  },
});
</script>
