<template>
	<div class="row">
		<div>
			<input
				v-model="newTodo"
				type="text"
			/>
			<button @click="addTodo">Add</button>
		</div>
		<span>Total: {{ todosCount }}</span>
		<div>
			<span>Filter by:</span>
			<select v-model="filter">
				<option value="all">All</option>
				<option value="completed">Completed</option>
				<option value="uncompleted">Uncompleted</option>
			</select>
		</div>
		<div>
			<span>limit:</span>
			<select v-model="limit">
				<option value="10">10</option>
				<option value="15">15</option>
				<option value="20">20</option>
			</select>
		</div>
		<ul
			v-if="todoList?.length"
			class="list"
		>
			<li
				v-for="todo in todoList"
				:key="todo.id"
				class="list__item"
			>
				<div>
					<input
						v-model="todo.completed"
						:checked="todo.completed"
						type="checkbox"
					/>
					<span
						v-if="editingTodoId !== todo.id"
						:class="{
							completed: todo.completed,
							uncompleted: !todo.completed,
						}"
						@dblclick="editingTodoId = todo.id"
					>
						{{ todo.title }}
					</span>
					<input
						v-else
						v-model="todo.title"
						type="text"
						@blur="editingTodoId = null"
					/>
				</div>
				<button @click="deleteTodo(todo)">X</button>
			</li>
		</ul>
		<div
			v-if="todosCount"
			class="pagination"
		>
			<button
				v-for="page in Math.ceil(todosCount / limit)"
				:key="page"
				:class="{ active: page === this.page }"
				@click="this.page = page"
			>
				{{ page }}
			</button>
		</div>
	</div>
</template>

<script>
import axios from 'axios';
export default {
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
		async deleteTodo(todo) {
			this.todoList = this.todoList.filter(item => item.id !== todo.id);
		},
		addTodo() {
			if (!this.newTodo) return;
			this.todoList.unshift({
				completed: false,
				id: Date.now(),
				title: this.newTodo,
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

	button {
		margin: 0 10px;
	}

	span,
	input[type='text'] {
		margin: 0 10px;
	}
}
ul {
	width: 700px;
	list-style-type: none;
	display: flex;
	flex-direction: column;
	margin: 0;
	padding: 0;
	border: 1px solid black;
}
li {
	display: flex;
	align-items: center;
	justify-content: space-between;
	padding: 10px 5px;
	text-transform: capitalize;
	&:nth-child(odd) {
		background-color: rgba(0, 0, 0, 0.1);
	}
}
.completed {
	color: rgb(41, 181, 41);
}
.uncompleted {
	color: orange;
}
.pagination {
	display: flex;
	align-items: center;
	justify-content: center;
	gap: 10px;
	margin-top: 10px;
	button {
		width: 30px;
		height: 30px;
		margin: 0;
		border-radius: 3px;
		border: 1px solid black;
		cursor: pointer;
		&.active {
			background: rgb(192, 211, 240);
			border: 1px solid blue;
			pointer-events: none;
		}
	}
}
</style>
