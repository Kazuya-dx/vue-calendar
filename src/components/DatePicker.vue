<template>
  <div>
    <button @click="goLastMonth()">前の月</button>
    <button @click="goNextMonth()">次の月</button>

    <!-- TODO: key の設定方法を見直す -->
    <div style="display: flex">
      <!-- 先月 -->
      <div style="margin: 0 4px">
        <div
          style="
            display: flex;
            justify-content: center;
            align-items: center;
            width: 275px;
            height: 40px;
            background-color: #aaa;
            color: #fff;
            font-weight: 600;
          "
        >
          {{ getYearMonth(getLastMonth(currentDate)) }}
        </div>
        <table style="background-color: #efefef">
          <thead>
            <th
              v-for="day in dayOfWeeks"
              :key="day"
              style="background-color: #fff; width: 35px; height: 35px"
            >
              {{ day }}
            </th>
          </thead>
          <tbody>
            <tr v-for="week in calendars.lastMonth" :key="'last-' + week">
              <td
                v-for="date in week"
                :key="'last-' + date"
                :class="{
                  'bg-base': isLastMonth(date),
                  'bg-gray': !isLastMonth(date),
                  'bg-red': isBasePeriod(date) && isLastMonth(date),
                  'bg-blue': isComparisonPeriod(date) && isLastMonth(date),
                }"
                style="width: 35px; height: 35px; text-align: center"
              >
                {{ getDay(date) }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- 今月 -->
      <div style="margin: 0 4px">
        <div
          style="
            display: flex;
            justify-content: center;
            align-items: center;
            width: 275px;
            height: 40px;
            background-color: #aaa;
            color: #fff;
            font-weight: 600;
          "
        >
          {{ getYearMonth(currentDate) }}
        </div>
        <table style="background-color: #efefef">
          <thead>
            <th
              v-for="day in dayOfWeeks"
              :key="day"
              style="background-color: #fff; width: 35px; height: 35px"
            >
              {{ day }}
            </th>
          </thead>
          <tbody>
            <tr v-for="week in calendars.thisMonth" :key="'this-' + week">
              <td
                v-for="date in week"
                :key="'this-' + date"
                :class="{
                  'bg-base': isThisMonth(date),
                  'bg-gray': !isThisMonth(date),
                  'bg-red': isBasePeriod(date) && isThisMonth(date),
                  'bg-blue': isComparisonPeriod(date) && isThisMonth(date),
                }"
                class="date"
                style="width: 35px; height: 35px; text-align: center"
              >
                {{ getDay(date) }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- 来月 -->
      <div style="margin: 0 4px">
        <div
          style="
            display: flex;
            justify-content: center;
            align-items: center;
            width: 275px;
            height: 40px;
            background-color: #aaa;
            color: #fff;
            font-weight: 600;
          "
        >
          {{ getYearMonth(getNextMonth(currentDate)) }}
        </div>
        <table style="background-color: #efefef">
          <thead>
            <th
              v-for="day in dayOfWeeks"
              :key="day"
              style="background-color: #fff; width: 35px; height: 35px"
            >
              {{ day }}
            </th>
          </thead>
          <tbody>
            <tr v-for="week in calendars.nextMonth" :key="'next-' + week">
              <td
                v-for="date in week"
                :key="'next-' + date"
                :class="{
                  'bg-base': isNextMonth(date),
                  'bg-gray': !isNextMonth(date),
                  'bg-red': isBasePeriod(date) && isNextMonth(date),
                  'bg-blue': isComparisonPeriod(date) && isNextMonth(date),
                }"
                class="date"
                style="width: 35px; height: 35px; text-align: center"
              >
                {{ getDay(date) }}
              </td>
            </tr>
          </tbody>
        </table>
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
      dayOfWeeks: ["月", "火", "水", "木", "金", "土", "日"],
      currentDate: moment().format("YYYY-MM-DD"),
      baseStart: "2022-07-12",
      baseEnd: "2022-07-26",
      comparisonStart: "2022-08-02",
      comparisonEnd: "2022-08-10",
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
    isThisMonth(targetDate: string) {
      return (
        moment(targetDate).format("YYYY-MM") ===
        moment(this.currentDate).format("YYYY-MM")
      );
    },
    isNextMonth(targetDate: string) {
      return (
        moment(targetDate).format("YYYY-MM") ===
        moment(this.currentDate).add(1, "month").format("YYYY-MM")
      );
    },
    isLastMonth(targetDate: string) {
      return (
        moment(targetDate).format("YYYY-MM") ===
        moment(this.currentDate).add(-1, "month").format("YYYY-MM")
      );
    },
    isBasePeriod(targetDate: string) {
      return moment(targetDate).isBetween(
        moment(this.baseStart),
        moment(this.baseEnd)
      );
    },
    isComparisonPeriod(targetDate: string) {
      return moment(targetDate).isBetween(
        moment(this.comparisonStart),
        moment(this.comparisonEnd)
      );
    },
    getLastMonth(targetDate: string) {
      return moment(targetDate).add(-1, "month").format("YYYY-MM-DD");
    },

    getNextMonth(targetDate: string) {
      return moment(targetDate).add(1, "month").format("YYYY-MM-DD");
    },

    getYearMonth(targetDate: string) {
      return moment(targetDate).format("YYYY年MM月");
    },

    getDay(targetDate: string) {
      return moment(targetDate).format("DD").replace(/^0+/, "");
    },

    /**
     * 対象日付が含まれるカレンダーの最初の日付(月曜日)を返す。
     * @param targetDate 対象日付
     */
    getStartDate(targetDate: string) {
      let date = moment(targetDate);
      // 月初を取得
      date.startOf("month");
      // 曜日を算出(日曜日:0, 月曜日:1, ...)
      const dayOfWeekNum = date.day();
      // 月初が日曜日の場合、１ヶ月前の「月曜日」を返す
      if (dayOfWeekNum === 0) return date.subtract(dayOfWeekNum + 6, "days");
      // 月初が日曜日以外の場合、その月の「月曜日」を返す
      else return date.subtract(dayOfWeekNum - 1, "days");
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

<style scoped>
.bg-base {
  background-color: #fff;
  cursor: pointer;
}
.bg-base:hover {
  background-color: orange;
}
.bg-gray {
  background-color: #efefef;
}
.bg-red {
  background-color: #f38181;
}
.bg-blue {
  background-color: #95e1d3;
}
.bg-orange:hover {
  background-color: orange;
}
</style>
