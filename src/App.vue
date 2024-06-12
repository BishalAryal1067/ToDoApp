<script setup>
import { ref, onMounted, onUpdated, watch, warn, watchEffect } from 'vue';

const taskCategory = ref(null);
const taskName = ref("");
const taskList = ref([]);
const categoryList = [
	{
		key: "wk",
		label: "work",
		value: "work",
	},
	{
		key: "pr",
		label: "personal",
		value: "personal",
	}
]


const showError = ref(false);

const isInputInvalid = () => {
	return taskCategory.value == null || taskName.value.trim() == "" ? true : false
}

const handleSubmit = () => {
	console.info(taskList.value)


	if (isInputInvalid()) {
		showError.value = true;
	}



	else {
		showError.value = false;
		const date = new Date()
		taskList.value.push({
			taskName: taskName.value,
			taskCategory: taskCategory.value,
			date: date.getDate() + "/" + (date.getMonth() + 1) + "/" + date.getFullYear(),
		})
		const localStorage = window.localStorage;
		localStorage.setItem('tasksCollection', JSON.stringify(taskList.value));
		taskName.value = "";
		taskCategory.value = null;
	}
}

onMounted(() => {
	taskList.value = JSON.parse(localStorage.getItem("tasksCollection"));
})

const removeTask = (task) => {
	taskList.value = taskList.value.filter(currentTask => currentTask != task);
	localStorage.setItem('tasksCollection', JSON.stringify(taskList.value));
}
</script>

<template>
	<article class="w-full lg:w-[40%] mx-auto my-5 lg:border-2 pb-12 lg:rounded-xl">
		<div class="px-6 py-5 flex flex-col gap-1">
			<p class="text-xl text-blue-700 font-semibold">Add a task</p>
			<input v-model="taskName" type="text" placeholder="Enter task name"
				class="border-[.15rem] border-gray-500 rounded-md px-2 py-1" :class="{ showError: 'border-red-400' }">
			<!--task categories select buttons-->
			<div class="flex gap-3 my-3">
				<div v-for="category in categoryList" @click="taskCategory = category.value"
					class="flex gap-1 items-center cursor-pointer border-[.15rem] border-gray-400 py-1 px-4 rounded-3xl">
					<input :value="category.value" type="radio" v-model="taskCategory" class="cursor-pointer">
					<p class="capitalize">{{ category.label }}</p>
				</div>
			</div>

			<span v-if="showError"
				class="block border-[.1rem] border-red-400 px-2 py-1 rounded-lg text-red-600 font-semibold">Fields
				cannot
				be left empty</span>

			<button class="bg-teal-700 text-white font-semibold py-2 rounded-xl max-w-fit px-12"
				@click="handleSubmit">Submit</button>
		</div>

		<hr class="mb-6">

		<input type="text"
			class="border-2 border-gray-400 rounded-3xl px-2 py-1 mx-6 max-w-full" placeholder="search task">

		<hr class="my-6">

		<section class="px-6">
			<h3 class="tex-lg font-semibold text-gray-700">TASK LIST</h3>
			<p v-if="taskList.length == 0"
				class="border-2 border-dashed flex justify-center py-2 my-5 text-gray-600 font-semibold">No tasks to
				show</p>

			<div v-for="task in taskList" v-else
				class="border-2 rounded-lg px-4 py-2 my-2 flex items-center justify-between">
				<div>
					<p class="text-lg text-gray-700 font-semibold"> {{ task.taskName }} </p>
					<p class="text-sm text-gray-500 font-medium">created on: {{ task.date }}</p>
				</div>
				<i class="fa-solid fa-trash text-red-500 cursor-pointer" @click="removeTask(task)"></i>
			</div>
		</section>
	</article>

	<style>
		article {
			display: flex;
			flex-direction: column;
			justify-content: center;
		}
	</style>
