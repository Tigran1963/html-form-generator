<script setup lang="ts">
import { computed, watch, ref } from 'vue'
import FormGenerator from './components/FormGenerator.vue'
import type { FormSchema } from './components/FormGenerator.vue'

const initialSchema: FormSchema = {
	"fields": [
		{ "type": "text", "label": "Имя", "model": "name", "required": true },
		{ "type": "email", "label": "Email", "model": "email", "required": true },
		{ "type": "password", "label": "Пароль", "model": "password", "required": true, "minLength": 6 },
		{ "type": "select", "label": "Роль", "model": "role", "options": ["Админ", "Пользователь"], "required": true },
		{ "type": "checkbox", "label": "Согласен с условиями", "model": "terms", "required": true }
	]
}
const formSchema = ref<FormSchema>(initialSchema)
const schemaInputString = ref(JSON.stringify(initialSchema, null, 2))

const formData = ref<Record<string, any>>({})
const formError = ref(false)

const handleSchemaInput = (val: string) => {
	try {
		const parsed = JSON.parse(val)
		formSchema.value = parsed
		formError.value = false
	} catch (e) {
		formError.value = true
	}
}
// Следим за изменениями в textarea
watch(schemaInputString, (newVal) => {
	handleSchemaInput(newVal)
})

const formDataString = computed(() => JSON.stringify(formData.value, null, 2))
</script>

<template>
	<div class="first-screen">
		<div class="first-screen__container">
			<h1 class="first-screen__title">Компонент генерации HTML формы</h1>
			<div class="first-screen__body">
				<div class="first-screen__column">
					<div class="first-screen__field">
						<label for="upload-json-textarea" class="first-screen__label">Вставьте JSON-схему</label>
						<textarea name="upload-json-textarea" id="upload-json-textarea" v-model="schemaInputString"
							class="first-screen__textarea" :class="{ 'first-screen__textarea--error': formError }"></textarea>
					</div>
					<div class="first-screen__field">
						<label for="reactive-form-data-textarea" class="first-screen__label">Состояние формы</label>
						<textarea name="reactive-form-data-textarea" id="reactive-form-data-textarea" readonly
							v-model="formDataString" class="first-screen__textarea"></textarea>
					</div>
				</div>
				<div class="first-screen__column">
					<div v-if="true" class="first-screen__column-title">Ваша форма</div>
					<FormGenerator :schema="formSchema" v-model="formData" />
				</div>
			</div>
		</div>
	</div>
</template>

<style scoped lang="scss">
.first-screen {
	padding: toRem(50) 0;

	&__title {
		font-weight: 600;
		font-size: toRem(40);
		text-align: center;
		margin-bottom: toRem(48);

		@media (max-width:$mobile) {
			font-size: toRem(32);
		}

		@media (max-width:$mobileSmall) {
			font-size: toRem(24);
		}
	}

	&__body {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: toRem(40);

		@media (max-width:$mobile) {
			grid-template-columns: 1fr;
		}
	}

	&__column-title {
		font-size: toRem(20);
		margin-bottom: toRem(16);
	}

	&__field {
		&:not(:last-child) {
			margin-bottom: toRem(32);
		}
	}

	&__label {
		font-size: toRem(20);
		margin-bottom: toRem(16);
	}

	&__textarea {
		height: toRem(250);
		border-radius: 8px;
		padding: toRem(16);
		resize: none;
		background-color: #fff;
		border: 2px solid $mainColor;

		&--error {
			border-color: red;
			outline-color: red;
		}
	}
}
</style>
