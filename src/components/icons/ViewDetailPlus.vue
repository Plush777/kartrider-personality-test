<template>
	<svg
		xmlns="http://www.w3.org/2000/svg"
		fill="currentColor"
		:width="props.width"
		:height="props.height"
		viewBox="0 0 32 32"
		:class="props.svgClassName"
	>
		<path
			d="M 16 3 C 8.832031 3 3 8.832031 3 16 C 3 23.167969 8.832031 29 16 29 C 23.167969 29 29 23.167969 29 16 C 29 8.832031 23.167969 3 16 3 Z M 16 5 C 22.085938 5 27 9.914063 27 16 C 27 22.085938 22.085938 27 16 27 C 9.914063 27 5 22.085938 5 16 C 5 9.914063 9.914063 5 16 5 Z M 15 10 L 15 15 L 10 15 L 10 17 L 15 17 L 15 22 L 17 22 L 17 17 L 22 17 L 22 15 L 17 15 L 17 10 Z"
		/>
	</svg>
</template>

<script setup>
const props = defineProps({
	svgClassName: {
		type: String,
		default: ''
	},
	width: {
		type: String,
		default: '24'
	},
	height: {
		type: String,
		default: '24'
	}
});
</script>
