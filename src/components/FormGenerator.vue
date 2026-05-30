<script setup lang="ts">
import { ref } from 'vue'

interface FormField {
	type: 'text' | 'email' | 'password' | 'select' | 'checkbox'
	label: string
	model: string
	required?: boolean
	minLength?: number
	pattern?: string
	options?: string[]
}
interface FormSchema {
	fields: FormField[]
}

const props = defineProps<{
	schema: FormSchema
}>()
const formData = defineModel<Record<string, any>>({ default: () => ({}) })
const errors = ref<Record<string, string>>({})

const handleSubmit = (event: Event) => {
	errors.value = {}
	let isValid = true

	props.schema.fields.forEach((field) => {
		const value = formData.value[field.model]

		// Проверка на Required
		if (field.required && (value === undefined || value === null || value === '' || value === false)) {
			errors.value[field.model] = `Поле "${field.label}" обязательно для заполнения`
			isValid = false
			return
		}
		// Проверка на MinLength
		if (field.minLength && typeof value === 'string' && value.length < field.minLength) {
			errors.value[field.model] = `Минимальная длина – ${field.minLength} символов`
			isValid = false
			return
		}
		// Проверка на Pattern (RegExp)
		if (field.pattern && typeof value === 'string') {
			const regex = new RegExp(field.pattern)
			if (!regex.test(value)) {
				errors.value[field.model] = `Поле не соответствует формату`
				isValid = false
			}
		}
	})

	if (isValid) {
		alert('Форма валидна!')
	}
}
</script>

<template>
	<form @submit.prevent="handleSubmit" class="form-generator">
		<div v-for="field in props.schema?.fields" :key="field.model" class="form-generator__field">
			<label :for="field.model" class="form-generator__label">
				{{ field.label }} <span v-if="field.required" class="form-generator__label-required-star">*</span>
			</label>

			<template v-if="field.type === 'select'">
				<select :id="field.model" v-model="formData[field.model]" class="form-generator__input">
					<option value="" disabled selected>Выберите значение</option>
					<option v-for="opt in field.options" :key="opt" :value="opt">
						{{ opt }}
					</option>
				</select>
			</template>

			<template v-else-if="field.type === 'checkbox'">
				<div class="form-generator__checkbox">
					<input type="checkbox" :id="field.model" v-model="formData[field.model]" />
					<label :for="field.model" class="form-generator__checkbox-text">Да</label>
				</div>
			</template>

			<template v-else>
				<input :type="field.type" :id="field.model" v-model="formData[field.model]" class="form-generator__input"
					:placeholder="`Введите ${field.label.toLowerCase()}`" />
			</template>

			<span v-if="errors[field.model]" class="form-generator__error">
				{{ errors[field.model] }}
			</span>
		</div>

		<button type="submit" class="form-generator__submit-btn">Проверить на валидность</button>
	</form>
</template>

<style scoped lang="scss">
.form-generator {
	display: flex;
	flex-direction: column;
	gap: toRem(24);
	padding: toRem(16);
	border: 1px solid $mainColor;
	border-radius: 8px;
	background: #fff;

	&__label-required-star {
		color: red;
	}

	&__field {
		display: flex;
		flex-direction: column;
		gap: toRem(8);
	}

	&__input {
		border-radius: 8px;
		padding: toRem(8) toRem(16);
		border: 1px solid $mainColor;
	}

	&__error {
		color: red;
		font-size: toRem(12);
		margin-top: toRem(4);
	}

	&__submit-btn {
		padding: toRem(8) toRem(24);
		background: $orangeColor;
		transition: all 0.3s;
		color: white;
		width: 100%;
		border: none;
		font-weight: 600;
		border-radius: toRem(16);
		cursor: pointer;

		&:hover {
			background: $orangeHoverColor;
		}
	}

	&__checkbox {
		display: flex;
		align-items: center;
		justify-content: end;

		label,
		input {
			cursor: pointer;
		}

		input {
			width: auto;
			margin-right: toRem(8);
		}
	}

	&__checkbox-text {}
}
</style>
