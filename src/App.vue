<script setup>
import { ref, onMounted } from 'vue';


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
	const storedTasks = localStorage.getItem("tasksCollection");
	if (storedTasks) {
		taskList.value = JSON.parse(storedTasks);
	}
});

const removeTask = (task) => {
	taskList.value = taskList.value.filter(currentTask => currentTask != task);
	localStorage.setItem('tasksCollection', JSON.stringify(taskList.value));
}
</script>

<template>
	<article class="w-full h-full lg:w-[40%] mx-auto my-5 lg:border-2 lg:rounded-xl">
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
			<!--submit task-->
			<button class="max-w-fit bg-blue-900 px-6 py-2 rounded-xl text-white font-semibold "
				@click="handleSubmit">Submit</button>

			<hr class="mt-2">
			<input type="text" class="border-2 border-gray-400 rounded-3xl px-2 py-1 my-3 max-w-full"
				placeholder="search task">

			<hr class="mb-6" />
			<section class="px-6">
				<h3 class="tex-lg font-semibold text-gray-700">TASK LIST</h3>

				<Transition name="empty">
					<p v-if="taskList.length == 0"
						class="border-2 border-dashed flex justify-center py-2 my-5 text-gray-600 font-semibold">No
						tasks to
						show</p>
				</Transition>




				<TransitionGroup name="list" tag="ul">
					<div v-if="taskList.length > 0" v-for="task in taskList"
						class="border-2 rounded-lg px-4 py-2 my-2 flex items-center justify-between">
						<div>
							<p class="text-lg text-gray-700 font-semibold"> {{ task.taskName }} </p>
							<p class="text-sm text-gray-500 font-medium">created on: {{ task.date }}</p>
						</div>
						<i class="fa-solid fa-trash text-red-500 cursor-pointer" @click="removeTask(task)"></i>
					</div>
				</TransitionGroup>
			</section>
		</div>
	</article>
</template>

<style>
article {
	display: flex;
	flex-direction: column;
	justify-content: center;
}

.list-move,
.list-enter-active,
.list-leave-active {
	transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
	opacity: 0;
	transform: translateX(30px);
}

.empty-enter-active,
.empty-leave-active {
	transition: all 0.35s ease;
}

.empty-enter-from,
.empty-leave-to {
	opacity: 0;
	transform: translateX(30px);
}


</style>
