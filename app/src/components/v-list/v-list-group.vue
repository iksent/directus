<template>
	<div class="v-list-group">
		<v-list-item
			class="activator"
			:active="active"
			:to="to"
			:exact="exact"
			:disabled="disabled"
			:dense="dense"
			@click="onClick"
		>
			<slot name="activator" :active="groupActive" />

			<v-list-item-icon class="activator-icon" :class="{ active: groupActive }" v-if="$slots.default">
				<v-icon name="chevron_right" @click.stop.prevent="toggle" :disabled="disabled" />
			</v-list-item-icon>
		</v-list-item>

		<div class="items" v-if="groupActive">
			<slot />
		</div>
	</div>
</template>

<script lang="ts">
import { defineComponent } from '@vue/composition-api';
import { useGroupable } from '@/composables/groupable';

export default defineComponent({
	props: {
		multiple: {
			type: Boolean,
			default: true,
		},
		to: {
			type: String,
			default: null,
		},
		active: {
			type: Boolean,
			default: false,
		},
		exact: {
			type: Boolean,
			default: false,
		},
		disabled: {
			type: Boolean,
			default: false,
		},
		scope: {
			type: String,
			default: undefined,
		},
		value: {
			type: [String, Number],
			default: undefined,
		},
		dense: {
			type: Boolean,
			default: false,
		},
	},
	setup(props, { listeners, emit }) {
		const { active: groupActive, toggle } = useGroupable({
			group: props.scope,
			value: props.value,
		});

		return { groupActive, toggle, onClick };

		function onClick(event: MouseEvent) {
			if (props.to) return null;
			if (listeners.click) return emit('click', event);

			event.stopPropagation();
			toggle();
		}
	},
});
</script>

<style lang="scss" scoped>
.v-list-group {
	margin-bottom: 4px;

	&:last-child {
		margin-bottom: 0;
	}

	.activator-icon {
		color: var(--foreground-subdued);
		transform: rotate(0deg);
		transition: transform var(--medium) var(--transition);

		&:hover {
			color: var(--foreground-normal);
		}

		&.active {
			transform: rotate(90deg);
		}
	}

	.items {
		padding-left: 16px;
	}
}
</style>
