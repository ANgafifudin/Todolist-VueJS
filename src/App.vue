<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				Halo Gaes <input type="text" id="name" placeholder="nama kamu" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3>TO DO LIST</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>Tuliskan to do yang kamu mau...</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="tulis disini"
					v-model="input_content" />
				
				<h4>Pilih Prioritas</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="low"
							v-model="input_category" />
						<span class="bubble low"></span>
						<div>Low</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="medium"
							v-model="input_category" />
						<span class="bubble medium"></span>
						<div>Medium</div>
					</label>
					<label>
						<input 
							type="radio" 
							name="category" 
							id="category3" 
							value="high"
							v-model="input_category" />
						<span class="bubble high"></span>
						<div>High</div>
					</label>
					<label>
						<input 
							type="radio" 
							name="category" 
							id="category4" 
							value="noPriority"
							v-model="input_category" />
						<span class="bubble noPriority"></span>
						<div>No Priority</div>
					</label>

				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TO DO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category === 'low' ? 'low' : 
							todo.category === 'medium' ? 'medium' : todo.category === 'high' ? 'high' : 'noPriority'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Hapus</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>
