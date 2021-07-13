<template>
  <div class="input-wrap">
	<p>{{title}}</p>
	<input
		v-if="type !== 'select' && type !== 'custom_color'" 
		:type="type" 
		:placeholder="placeholder"
		:value="value"
		@input="$emit('input', $event.target.value)"
		class="input-text"
	>

	<select v-if="type === 'select'" @change="$emit('input', $event.target.value)">
		<option selected disabled>Выбери фигуру</option>
		<option v-for="option in options" :key="option" :value="option">{{option}}</option>
	</select>

	<div v-if="type === 'custom_color'" class="colors">
		<label v-for="color in colors"
			:key="color"
			class="colors__round"
			:class="{colors__round_active: activeColor === color}"
			:style="{'background-color': color}"
		>
			<input type="radio"
				class="colors__radio"
				:value="color"
				name="color-button"
				@change="$emit('input', $event.target.value)"
			/>
		</label>
	</div>
  </div>
</template>

<script>
export default {
  name: 'Input',
  props: {
    title: String,
	type: {
		type: String,
		default: 'text'
	},
	placeholder: String,
	options: Array,
	value: [String, Number],
	colors: Array,
	activeColor: String
  }
}
</script>

<style scoped lang="scss">
.input-wrap {
	margin-right: 10px;
}
.input-text {
	max-width: 100px;
}
.colors {
	display: flex;
	&__round {
		width: 20px;
		height: 20px;
		border-radius: 50%;
		margin-right: 5px;
		cursor: pointer;
		opacity: 0.5;
		&_active {
			opacity: 1;
		}
	}
	&__radio {
		display: none;
	}
}

</style>
