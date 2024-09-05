<template>
	<div class="row">
		<span>Total: {{ todosCount }}</span>
		<TheForm
			v-model:filter="filter"
			v-model:limit="limit"
			@add-todo="addTodo"
		/>
		<TodoList
			v-if="todoList?.length"
			:list="todoList"
			@delete-todo="deleteTodo"
		/>
		<ThePagination
			v-if="todosCount"
			v-model:page="page"
			:pages-count="pagesCount"
		/>
	</div>
</template>

<script>
import axios from 'axios';

import TheForm from './components/TheForm.vue';
import TodoList from './components/TodoList.vue';
import ThePagination from './components/ThePagination.vue';

export default {
	components: {
		TheForm,
		TodoList,
		ThePagination,
	},
	data() {
		return {
			baseUrl: 'https://jsonplaceholder.typicode.com/',
			todoList: [],
			newTodo: '',
			filter: 'all',
			editingTodoId: null,
			limit: 10,
			page: 1,
			todosCount: null,
		};
	},
	async created() {
		await this.getTodos();
		await this.getTodos({
			_limit: this.limit,
			_page: this.page,
		});
	},
	computed: {
		isFiltered() {
			return this.filter !== 'all';
		},
		pagesCount() {
			return Math.ceil(this.todosCount / this.limit);
		},
	},
	watch: {
		async limit() {
			await this.getTodosBy();
		},
		async page() {
			await this.getTodosBy();
		},
		async filter() {
			this.page = 1;
			// якщо відфільтровано
			if (this.isFiltered) {
				// для пагінації
				await this.getTodos({
					completed: this.filter === 'completed',
				});
				// для списку
				await this.getTodos({
					_limit: this.limit,
					_page: this.page,
					completed: this.filter === 'completed',
				});
			}
			// якщо не відфільтровано
			if (this.filter === 'all') {
				// для пагінації
				await this.getTodos();
				// для списку
				await this.getTodos({
					_limit: this.limit,
					_page: this.page,
				});
			}
		},
	},
	methods: {
		async getTodosBy() {
			const params = {
				_limit: this.limit,
				_page: this.page,
			};
			// якщо відфільтровано - додаємо параметр
			if (this.isFiltered) {
				params.completed = this.filter === 'completed';
			}
			await this.getTodos(params);
		},
		async getTodos(params = null) {
			try {
				const { data } = await axios.get(`${this.baseUrl}todos`, { params });
				// для отримання загальної кількості
				if (!params || !params?._limit) {
					this.todosCount = data.length;
				}
				// запис тудушок
				else this.todoList = data;
			} catch (error) {
				console.log('getTodos ~ error:', error);
			}
		},
		async deleteTodo(todoId) {
			this.todoList = this.todoList.filter(item => item.id !== todoId);
		},
		addTodo(todo) {
			if (!todo) return;
			this.todoList.unshift({
				completed: false,
				id: Date.now(),
				title: todo,
			});
		},
	},
};
</script>

<style scoped lang="scss">
.row {
	font-family: Arial, 'Helvetica Neue', Helvetica, sans-serif;
	display: flex;
	align-items: center;
	justify-content: center;
	flex-direction: column;
}
</style>
