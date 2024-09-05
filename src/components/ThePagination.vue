<template>
	<div class="pagination">
		<button
			v-for="page in pagesCount"
			:key="page"
			:class="{ active: page === this.page }"
			@click="currentPage = page"
		>
			{{ page }}
		</button>
	</div>
</template>

<script>
export default {
	name: 'ThePagination',
	props: {
		pagesCount: {
			type: Number,
			default: 1,
		},
		page: {
			type: Number,
			default: 1,
		},
	},
	emits: ['update:page'],
	computed: {
		currentPage: {
			get() {
				return this.page;
			},
			set(value) {
				this.$emit('update:page', value);
			},
		},
	},
};
</script>

<style lang="scss" scoped>
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
