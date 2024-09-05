<template>
	<form>
		<div>
			<input
				v-model="newTodo"
				type="text"
			/>
			<button @click.prevent="addTodo">Add</button>
		</div>
		<div>
			<span>Filter by:</span>
			<select v-model="selectedFilter">
				<option value="all">All</option>
				<option value="completed">Completed</option>
				<option value="uncompleted">Uncompleted</option>
			</select>
		</div>
		<div>
			<span>limit:</span>
			<select v-model="selectedLimit">
				<option :value="10">10</option>
				<option :value="15">15</option>
				<option :value="20">20</option>
			</select>
		</div>
	</form>
</template>

<script>
export default {
	name: 'TheForm',
	props: {
		filter: {
			type: String,
			default: 'all',
		},
		limit: {
			type: Number,
			default: 10,
		},
	},
	emits: ['add-todo', 'update:filter', 'update:limit'],
	data() {
		return {
			newTodo: '',
		};
	},
	computed: {
		selectedFilter: {
			get() {
				return this.filter;
			},
			set(value) {
				this.$emit('update:filter', value);
			},
		},
		selectedLimit: {
			get() {
				return this.limit;
			},
			set(value) {
				this.$emit('update:limit', value);
			},
		},
	},
	methods: {
		addTodo() {
			this.$emit('add-todo', this.newTodo);
			this.newTodo = '';
		},
	},
};
</script>

<style lang="scss" scoped>
button {
	margin: 0 10px;
}

span,
input[type='text'] {
	margin: 0 10px;
}
</style>
