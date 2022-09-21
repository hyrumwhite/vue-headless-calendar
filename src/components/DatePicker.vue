<template>
	<div class="border rounded-md p-2">
		<Calendar>
			<template #monthSelect="{ month, year, onNextMonth, onPreviousMonth }">
				<div class="flex items-center justify-between mb-1 font-bold">
					<button icon @click="onPreviousMonth">&lt;</button>
					<span>{{ month }} {{ year }}</span>
					<button icon @click="onNextMonth">&gt;</button>
				</div>
			</template>
			<template #daysHeader="{ days }">
				<div class="font-bold rounded-md">
					<span
						v-for="day of days"
						:key="day"
						class="w-9 inline-flex justify-center items-center"
						>{{ day }}</span
					>
				</div>
			</template>
			<template #weeks="{ selectedDay, setSelectedDay, weeks }">
				<CalendarWeeks :selectedDay="selectedDay" :weeks="weeks">
					<template
						#day="{ day, notCurrentMonth, selected, today, disabled, title }"
					>
						<button
							class="
								w-9
								h-9
								inline-flex
								items-center
								justify-center
								hover:rounded-full
								text-slate-800
								dark:text-slate-300
								hover:bg-blue-100
								dark:hover:bg-slate-700
								cursor-pointer
								transition-[background]
								ease-out
								delay
								disabled:cursor-not-allowed disabled:bg-zinc-100
								dark:disabled:bg-zinc-700
								disabled:rounded-none disabled:text-slate-400
								dark:disabled:text-slate-500
							"
							:title="today ? `Today: ${title}` : title"
							:disabled="disabled"
							:class="{
								'text-opacity-50 dark:text-opacity-40': notCurrentMonth,
								'border-2 border-blue-600 rounded-md  font-bold': today,
								'rounded-full': !today,
								'hover:bg-blue-600 bg-blue-600 text-slate-100 font-bold rounded-full':
									selected,
							}"
							@click="setSelectedDay(day)"
						>
							{{ day.day }}
						</button>
					</template>
				</CalendarWeeks>
			</template>
		</Calendar>
	</div>
</template>

<script setup>
import Calendar from "./Calendar.vue";
import CalendarWeeks from "./CalendarWeeks.vue";
</script>

