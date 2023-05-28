<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const tasks = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const tasks_asc = computed(() => tasks.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(tasks, (newVal) => {
	localStorage.setItem('tasks', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		alert('Fields cannot be left empty !');
	}
	else{
		tasks.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})

	input_content.value = '';
	input_category.value= null;
	}

	
}

const removeTodo = (todo) => {
	tasks.value = tasks.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	tasks.value = JSON.parse(localStorage.getItem('tasks')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h3 class="title">
				Hello there, <input type="text" id="name" placeholder="Enter your name here" v-model="name">
			</h3>
		</section>

		<section class="create-todo">
			<form id="new-todo-form" @submit.prevent="addTodo">
				<h3>Add a task</h3>
				<h4>Planning to do something?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="Example: Make a video"
					v-model="input_content" />
				
				<h4 class="option-heading">Select an option</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="work"
							v-model="input_category" />
						<div>Work</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>


					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="other"
							v-model="input_category" />
						<div>Other</div>
					</label>
				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<hr>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in tasks_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>
