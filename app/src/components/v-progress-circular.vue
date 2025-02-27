<script setup lang="ts">
import { computed } from 'vue';
import { useSizeClass } from '@directus/composables';

interface Props {
	/** If set to true displays no value but spins indefinitely */
	indeterminate?: boolean;
	/** Which value to represent going from 0 to 100 */
	value?: number;
	/** Renders the progress circular smaller */
	xSmall?: boolean;
	/** Renders the progress circular small */
	small?: boolean;
	/** Renders the progress circular large */
	large?: boolean;
	/** Renders the progress circular larger */
	xLarge?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
	indeterminate: false,
	value: 0,
});

defineEmits(['animationiteration']);

const sizeClass = useSizeClass(props);

const circleStyle = computed(() => ({
	'stroke-dasharray': (props.value / 100) * 78.5 + ', 78.5',
}));
</script>

<template>
	<div class="v-progress-circular" :class="sizeClass">
		<svg
			class="circle"
			viewBox="0 0 30 30"
			:class="{ indeterminate }"
			@animationiteration="$emit('animationiteration', $event)"
		>
			<path
				class="circle-background"
				d="M12.5,0A12.5,12.5,0,1,1,0,12.5,12.5,12.5,0,0,1,12.5,0Z"
				transform="translate(2.5 2.5)"
			/>
			<path
				class="circle-path"
				:style="circleStyle"
				d="M12.5,0A12.5,12.5,0,1,1,0,12.5,12.5,12.5,0,0,1,12.5,0Z"
				transform="translate(2.5 2.5)"
			/>
		</svg>
		<slot />
	</div>
</template>

<style>
body {
	--v-progress-circular-color: var(--theme--foreground);
	--v-progress-circular-background-color: var(--theme--form--field--input--border-color);
	--v-progress-circular-transition: 400ms;
	--v-progress-circular-speed: 2s;
	--v-progress-circular-size: 28px;
	--v-progress-circular-line-size: 3px;
}
</style>

<style lang="scss" scoped>
.v-progress-circular {
	position: relative;
	display: flex;
	align-items: center;
	justify-content: center;
	width: var(--v-progress-circular-size);
	height: var(--v-progress-circular-size);

	&.x-small {
		--v-progress-circular-size: 12px;
		--v-progress-circular-line-size: 4px;
	}

	&.small {
		--v-progress-circular-size: 20px;
		--v-progress-circular-line-size: 3px;

		margin: 2px;
	}

	&.large {
		--v-progress-circular-size: 48px;
		--v-progress-circular-line-size: 2.5px;
	}

	&.x-large {
		--v-progress-circular-size: 64px;
		--v-progress-circular-line-size: 2px;
	}

	.circle {
		position: absolute;
		top: 0;
		left: 0;
		width: var(--v-progress-circular-size);
		height: var(--v-progress-circular-size);

		&-path {
			transition: stroke-dasharray var(--v-progress-circular-transition) ease-in-out;
			fill: transparent;
			stroke: var(--v-progress-circular-color);
			stroke-width: var(--v-progress-circular-line-size);
		}

		&.indeterminate {
			animation: rotate var(--v-progress-circular-speed) infinite linear;

			.circle-path {
				animation: stroke var(--v-progress-circular-speed) infinite linear;
			}
		}

		&-background {
			fill: transparent;
			stroke: var(--v-progress-circular-background-color);
			stroke-width: var(--v-progress-circular-line-size);
		}
	}
}

@keyframes rotate {
	0% {
		transform: rotate(0deg);
	}

	50% {
		transform: rotate(360deg);
	}

	100% {
		transform: rotate(1080deg);
	}
}

@keyframes stroke {
	0% {
		stroke-dasharray: 0, 78.5px;
	}

	50% {
		stroke-dasharray: 78.5px, 78.5px;
	}

	100% {
		stroke-dasharray: 0, 78.5px;
	}
}
</style>
