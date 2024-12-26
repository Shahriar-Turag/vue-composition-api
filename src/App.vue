<script setup>
import { onMounted, ref } from 'vue';

const name = ref('John doe');
const status = ref('active');
const newTask = ref(' ');

const tasks = ref([
	{ id: 1, title: 'Task 1', status: 'completed' },
	{ id: 2, title: 'Task 2', status: 'pending' },
	{ id: 3, title: 'Task 3', status: 'invalid' },
	{ id: 4, title: 'Task 4', status: 'completed' },
]);

const toggleStatus = () => {
	if (status.value === 'active') {
		status.value = 'pending';
	} else if (status.value === 'pending') {
		status.value = 'inactive';
	} else {
		status.value = 'active';
	}
};

const addTask = () => {
	if (!newTask.value.trim()) {
		// Prevent adding if input is empty
		console.warn('Task title cannot be empty!');
		return;
	}

	tasks.value.push({
		id: tasks.value.length + 1,
		title: newTask.value,
		status: 'pending',
	});
	newTask.value = ''; // Clear the input after adding
};

const deleteTask = (id) => {
	tasks.value = tasks.value.filter((task) => task.id !== id);
};

onMounted(async () => {
	try {
		const res = await fetch('https://jsonplaceholder.typicode.com/todos');
		const data = await res.json();
		tasks.value = data.map((task) => ({
			id: task.id,
			title: task.title,
			status: task.completed ? 'completed' : 'pending',
		}));
	} catch (error) {
		console.error('Failed to fetch tasks', error);
	}
});
</script>

<template>
	<h1>{{ name }}</h1>
	<p class="green" v-if="status === 'active'">User is {{ status }}</p>
	<p class="yellow" v-else-if="status === 'pending'">User is Pending</p>
	<p class="red" v-else>User is Inactive</p>

	<h3>{{ name }}'s Tasks</h3>
	<ul>
		<li v-for="task in tasks" :key="task.id">
			<span>
				{{ task.title }} -
				{{
					task.status === 'completed'
						? 'Completed'
						: task.status === 'pending'
						? 'Pending'
						: 'Invalid'
				}}
			</span>

			<!-- drop down to change task status -->
			<select v-model="task.status">
				<option value="completed">Completed</option>
				<option value="pending">Pending</option>
				<option value="invalid">Invalid</option>
			</select>
			<button @click="deleteTask(task.id)">X</button>
		</li>
	</ul>

	<form @submit.prevent="addTask">
		<label for="newTask">Add Task: </label>
		<input type="text" v-model="newTask" id="newTask" name="newTask" />
		<button type="submit">Add</button>
	</form>

	<button @click="toggleStatus">Change status</button>
</template>
