<template lang="pug">
.dropdown
	//- Label
	VEcoText.dropdown__label(:label="label")

	//- Input Field
	.dropdown__container(
		@click="toggle",
		:class="[{ 'dropdown__container--outline': settings.outline, 'dropdown__container--active': open }, `dropdown__container--theme-${settings.theme}`]"
	)
		VEcoText.dropdown__container--placeholder(
			v-if="selected === null",
			:label="placeholder"
		)
		VEcoText.dropdown__container--text(v-else, :label="options[selected].label")
		VEcoIcon.dropdown__container--icon(
			:type="open ? 'ri-arrow-up-s-line' : 'ri-arrow-down-s-line'"
		)

	//- Drop Area
	.dropdown__content(
		v-if="open && options.length",
		@mouseleave="toggle",
		:class="[settings.contain ? 'dropdown__content--contain' : '', `dropdown__content--flow-${settings.flow}`, settings.outline ? 'dropdown__content--outline' : '', `dropdown__content--theme-${settings.theme}`]"
	)
		.dropdown__option(
			v-for="(option, index) in options",
			@click="handleSelection(index)",
			:class="[`dropdown__option--theme-${settings.theme}`]"
		)
			VEcoText.dropdown__option--text(
				:class="[{ 'dropdown__option--active': index === selected }]",
				:label="option.label"
			)
			VEcoDot.dropdown__option--dot(type="information", v-if="index === selected")
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";
import { choice_option, dropdown_config } from "@/plugin/utils/types.interface";
import config from "@/plugin/utils/defaults/components/dropdown.config";
import VEcoDot from "@/plugin/components/common/dot.vue";
import VEcoText from "@/plugin/components/common/text.vue";
import VEcoIcon from "@/plugin/components/common/icon.vue";

export default defineComponent({
	name: "Dropdown",
	props: {
		label: {
			type: String as PropType<string>,
			default: "",
		},
		placeholder: {
			type: String as PropType<string>,
			default: "",
		},
		options: {
			type: Object as PropType<choice_option[]>,
			required: true,
		},
		config: {
			type: Object as PropType<dropdown_config>,
			default: () => config,
		},
		defaultIndex: {
			type: Number as PropType<number>,
			default: null,
		},
		defaultValue: {
			type: [String, Number] as PropType<string | number>,
			default: null,
		},
	},
	components: {
		VEcoDot,
		VEcoText,
		VEcoIcon,
	},
	computed: {
		settings(): dropdown_config {
			return Object.assign({ ...config }, this.config);
		},
	},
	data() {
		return {
			open: false as boolean,
			selected: null,
		};
	},
	methods: {
		toggle(): void {
			this.open = !this.open;
		},
		handleSelection(index: number): void {
			this.selected = index;
			this.toggle();
			this.$emit("change", this.options[index].value);
		},
	},
	mounted() {
		this.selected = this.defaultIndex;
		if (this.defaultValue) {
			this.selected = this.options.findIndex(
				(option: choice_option) => option.value === this.defaultValue
			);
		}
	},
});
</script>

<style lang="scss" scoped>
.dropdown {
	user-select: none;
	position: relative;
	width: 100%;

	&__label {
		@include font-sidenote;
		margin: 0;
	}

	&__container {
		@include input-container;
	}

	&__content {
		@include dropdown-content-area;
	}

	&__option {
		padding: $spacer-0-125 $spacer-0-25;
		border-radius: $border-radius-standard;
		display: flex;
		align-items: center;
		column-gap: $spacer-0-5;
		@include use-theme;

		&--active {
			font-weight: $font-weight-medium;
		}
	}
}
</style>
