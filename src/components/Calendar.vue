<template>
	<div>
		<slot
			name="monthSelect"
			:onPreviousMonth="previousMonth"
			:onNextMonth="nextMonth"
			:month="monthName"
			:year="currentYear"
			:onMonthClick="showMonthSelect"
			:onYearClick="showYearSelect"
		>
		</slot>
		<slot name="daysHeader" :days="days"></slot>
		<slot
			name="weeks"
			:weeks="weeks"
			:selectedDay="selectedDay"
			:setSelectedDay="selectDay"
		></slot>
	</div>
</template>

<script setup>
import { ref, watch } from "vue";
const days = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
const props = defineProps({
	modelValue: {
		type: Number,
		default: Date.now(),
	},
	min: {
		type: Number,
		default: null,
	},
	max: {
		type: Number,
		default: null,
	},
	displayedDate: {
		type: Number,
		default: null,
	},
});
const emit = defineEmits(["update:modelValue"]);
const monthName = ref("");
const weeks = ref([]);
const currentMonth = ref(0);
const currentDay = ref(0);
const currentYear = ref(2017);
const selectedDay = ref({});
const getDaysInMonth = (month, year) => new Date(year, month + 1, 0).getDate();
const generateCalendar = (date) => {
	weeks.value = [[], [], [], [], [], []];
	date = date || new Date();
	monthName.value = date.toLocaleString("en-us", { month: "long" });
	let year = date.getFullYear();
	let month = date.getMonth();
	currentYear.value = year;
	currentMonth.value = month;
	currentDay.value = date.getDay();
	let days = getDaysInMonth(currentMonth.value, year);
	let previousMonth = currentMonth.value - 1;
	let previousDays = getDaysInMonth(
		previousMonth,
		previousMonth === 0 ? year - 1 : year
	);
	let firstDay = new Date(year, currentMonth.value, 1);
	weeks.value[0] = [];
	let previousDayCount = previousDays - firstDay.getDay() + 1;
	let dayCount = 1;
	let previousYear = year;
	if (previousMonth === 11 || previousMonth === -1) {
		previousMonth = 11;
		previousYear = year - 1;
	}
	let nextMonth = currentMonth.value + 1;
	let nextYear = year;
	if (nextMonth % 12 === 0) {
		nextMonth = 0;
		nextYear = year + 1;
	}
	let nextMonthDayCount = 1;
	for (let i = 0; i < 6; i++) {
		for (let j = 0; j < 7; j++) {
			let date = new Date();
			if (previousDayCount <= previousDays) {
				date.setDate(previousDayCount);
				date.setMonth(previousMonth);
				date.setYear(previousYear);
				weeks.value[i][j] = {
					day: previousDayCount,
					notCurrentMonth: true,
					month: previousMonth,
					previousMonth: true,
					year: previousYear,
					disabled:
						(props.max != null && date > props.max) ||
						(props.min != null && date < props.min),
				};
				previousDayCount++;
			} else if (dayCount <= days) {
				date.setDate(dayCount);
				date.setMonth(currentMonth.value);
				date.setYear(year);
				weeks.value[i][j] = {
					day: dayCount,
					month: currentMonth.value,
					year,
					disabled:
						(props.max != null && date > props.max) ||
						(props.min != null && date < props.min),
				};
				dayCount++;
			} else {
				date.setDate(nextMonthDayCount);
				date.setMonth(nextMonth);
				date.setYear(nextYear);
				weeks.value[i][j] = {
					day: nextMonthDayCount,
					notCurrentMonth: true,
					nextMonth: true,
					month: nextMonth,
					year: nextYear,
					disabled:
						(props.max != null && date > props.max) ||
						(props.min != null && date < props.min),
				};
				nextMonthDayCount++;
			}
		}
	}
};

//using immediate watch instead of watcheffect bc we don't want this to run when selected day changes
watch(
	() => props.modelValue,
	() => {
		let value = null;
		if (props.modelValue === null) {
			selectedDay.value = {};
			value = new Date();
		} else {
			value = new Date(props.modelValue);
			selectedDay.value = { day: value.getDate(), month: value.getMonth() };
		}
		generateCalendar(value);
	},
	{ immediate: true }
);

watch(
	() => props.displayedDate,
	() => {
		generateCalendar(new Date(props.displayedDate));
	}
);

const showMonthSelect = () => {};
const showYearSelect = () => {};

const selectDay = (day) => {
	let previousSelection = selectedDay.value;
	selectedDay.value = day;
	if (day.previousMonth) {
		previousMonth();
	} else if (day.nextMonth) {
		nextMonth();
	}
	let date = new Date();
	date.setDate(day.day);
	date.setMonth(day.month);
	date.setYear(day.year);
	//TODO: min/max
	// if (this.onlyFuture && date < new Date()) {
	// 	selectedDay.value = previousSelection;
	// 	return;
	// }
	emit("update:modelValue", date.getTime());
};

const previousMonth = () => {
	let originalYear = currentYear.value;
	let originalMonth = currentMonth.value;
	if (currentMonth.value === 0) {
		currentYear.value -= 1;
		currentMonth.value = 11;
	} else {
		currentMonth.value -= 1;
	}
	let previousMonth = new Date(currentYear.value, currentMonth.value);
	let today = new Date();
	// implement min/max
	// if (
	// 	this.onlyFuture &&
	// 	previousMonth.getMonth() < today.getMonth() &&
	// 	previousMonth.getFullYear() <= today.getFullYear()
	// ) {
	// 	currentMonth.value = originalMonth;
	// 	currentYear.value = originalYear;
	// 	return;
	// }
	generateCalendar(previousMonth);
};
const nextMonth = () => {
	if (currentMonth.value === 11) {
		currentYear.value += 1;
		currentMonth.value = 0;
	} else {
		currentMonth.value += 1;
	}
	let nextMonth = new Date(currentYear.value, currentMonth.value);
	generateCalendar(nextMonth);
};
</script>