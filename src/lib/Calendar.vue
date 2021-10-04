<template>
  <div class="flex items-center mb-4">
    <div v-for="i in days" :key="i">{{ format(i, "D") }}</div>
  </div>
</template>

<script>
import {
  format,
  startOfMonth,
  lastDayOfMonth,
  addDays,
  subDays,
  startOfDay,
  addMonths,
  subMonths,
  isEqual,
  getDaysInMonth
} from "date-fns";

export default {
  name: "Calendar",
  props: {
    modelValue: {
      type: Date,
      required: true,
      default: new Date()
    }
  },
  emits: ["update:modelValue"],
  data() {
    return {
      firstDayInMonth: startOfMonth(new Date(this.modelValue))
    };
  },
  computed: {
    daysInCurrentMonth() {
      let days = [];
      for (let i = 0; i < getDaysInMonth(this.firstDayInMonth); i++) {
        days.push(addDays(this.firstDayInMonth, i));
      }
      return days;
    },
    daysInPreviewPreviousMonth() {
      let days = [];
      for (let i = 1; i <= this.firstDayInMonth.getDay(); i++) {
        days.unshift(subDays(this.firstDayInMonth, i));
      }
      return days;
    },
    daysInPreviewNextMonth() {
      let days = [];
      const gridsize = 42;
      const loop =
        gridsize -
        this.daysInCurrentMonth.length -
        this.daysInPreviewPreviousMonth.length;
      for (let i = 1; i <= loop; i++) {
        days.push(addDays(startOfDay(lastDayOfMonth(this.firstDayInMonth)), i));
      }

      return days;
    },
    days() {
      return [
        ...this.daysInPreviewPreviousMonth,
        ...this.daysInCurrentMonth,
        ...this.daysInPreviewNextMonth
      ];
    }
  },
  methods: {
    format: format,
    goNextMonth() {
      this.firstDayInMonth = startOfMonth(addMonths(this.firstDayInMonth, 1));
    },
    goPrevMonth() {
      this.firstDayInMonth = startOfMonth(subMonths(this.firstDayInMonth, 1));
    },
    selectDate(date) {
      this.$emit("update:modelValue", date);
      if (!this.isCurrentMonth(date)) {
        this.firstDayInMonth = startOfMonth(date);
      }
    },
    isCurrentMonth(date) {
      return (
        new Date(date).getMonth() === new Date(this.firstDayInMonth).getMonth()
      );
    },
    isToday(date) {
      return isEqual(new Date(date), startOfDay(new Date()));
    },
    isSelected(date) {
      return isEqual(new Date(date), new Date(this.modelValue));
    }
  }
};
</script>

<style lang="postcss" scoped>
.days {
  @apply text-sm text-center w-7 p-1 mx-auto rounded-full focus:outline-none;
}
.days.today {
  @apply underline;
}
.days.selected {
  @apply bg-blue-500 text-white;
}
.days.not-current-month {
  @apply text-gray-400;
}
</style>
