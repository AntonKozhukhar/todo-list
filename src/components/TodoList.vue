<template>
	<ul class="list">
		<TodoItem
			v-for="todo in list"
			:key="todo.id"
			:todo="todo"
			@delete-todo="$emit('delete-todo', $event)"
		/>
	</ul>
</template>

<script>
import TodoItem from './TodoItem.vue';
export default {
	components: { TodoItem },
	name: 'TodoList',
	props: {
		list: {
			type: Array,
			default: () => [],
		},
	},
};
</script>

<style lang="scss" scoped>
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
</style>
