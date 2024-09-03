<template>
	<li>
		<div>
			<input
				v-model="todo.completed"
				:checked="todo.completed"
				type="checkbox"
			/>
			<input
				v-if="isEditing"
				v-model="todo.title"
				type="text"
				@blur="isEditing = false"
			/>
			<span
				v-else
				:class="{
					completed: todo.completed,
					uncompleted: !todo.completed,
				}"
				@dblclick="isEditing = true"
			>
				{{ todo.title }}
			</span>
		</div>
		<button @click="$emit('delete-todo', todo.id)">X</button>
	</li>
</template>

<script>
export default {
	name: 'TodoList',
	props: {
		todo: {
			type: Object,
			default: () => {},
		},
	},
	data() {
		return {
			isEditing: false,
		};
	},
};
</script>

<style lang="scss" scoped>
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
