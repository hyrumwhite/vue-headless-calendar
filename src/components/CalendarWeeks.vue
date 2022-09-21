<template>
	<div v-for="(week, index) of weeks" :key="index">
		<slot
			v-for="day of week"
			:key="`${day.day}-${day.month}`"
			name="day"
			class="day"
			:day="day"
			:notCurrentMonth="day.notCurrentMonth"
			:selected="equalDays(day, selectedDay)"
			:today="equalDays(day, today)"
			:disabled="day.disabled"
			:title="`${day.month + 1}/${day.day}/${day.year}`"
			:onClick="() => emit('daySelected', day)"
		/>
	</div>
</template>

<script setup>
const props = defineProps({
	selectedDay: {
		type: Object,
		default: null,
	},
	weeks: {
		type: Array,
		default: [],
	},
});
const emit = defineEmits(["daySelected"]);
let date = new Date();
const today = { day: date.getDate(), month: date.getMonth() };
const equalDays = (day1, day2) => {
	return day1.month === day2.month && day1.day === day2.day;
};
</script>
