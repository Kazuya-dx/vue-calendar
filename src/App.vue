<template>
  <div id="app">
    <h1>calendar</h1>
    <date-picker />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import DatePicker from "@/components/DatePicker.vue";

export default Vue.extend({
  name: "App",
  components: {
    DatePicker,
  },
});
</script>
