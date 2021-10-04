<template>
  <div class="flex flex-col">
    <div class="">
      Hello World
    </div>
    <div>
      <Calendar />
    </div>
  </div>
</template>

<script>
import Calendar from "../lib/Calendar.vue";
export default {
  components: {
    Calendar
  }
};
</script>
