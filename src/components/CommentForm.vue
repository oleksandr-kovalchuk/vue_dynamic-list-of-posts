<script setup>
import { ref } from 'vue'
import InputField from './InputField.vue'
import TextAreaField from './TextAreaField.vue'

defineProps({
  error: String,
})

const emit = defineEmits(['createComment', 'closeForm'])

const fields = ref({
  name: '',
  email: '',
  body: '',
})

const errors = ref({
  name: '',
  email: '',
  body: '',
})

const handleRemoveError = (key) => {
  errors.value[key] = ''
}

const handleCheckErrors = () => {
  if (!fields.value.name) {
    errors.value.name = `Name is required!`
  }
  if (!fields.value.email) {
    errors.value.email = `Email is required!`
  }
  if (!fields.value.body) {
    errors.value.body = `Comment is required!`
  }

  const emailPattern = /^[\w.-]+@[a-zA-Z\d.-]+\.[a-zA-Z]{2,}$/
  if (!errors.value.email && !emailPattern.test(fields.value.email)) {
    errors.value.email = `Email is no valid!`
  }
}

const handleSubmit = () => {
  handleCheckErrors()

  if (Object.values(errors.value).some((error) => error.length > 0)) {
    return
  }

  emit('createComment', { ...fields.value })
}

const handleReset = () => {
  emit('closeForm')
}
</script>

<template>
  <form @submit.prevent="handleSubmit" @reset="handleReset" novalidate>
    <InputField
      v-model="fields.name"
      :input-name="'commentAuthor'"
      :input-error="errors.name"
      @removeError="handleRemoveError('name')"
    />
    <InputField
      v-model="fields.email"
      :input-name="'commentAuthorEmail'"
      :input-error="errors.email"
      @removeError="handleRemoveError('email')"
    />
    <TextAreaField
      v-model="fields.body"
      :text-area-name="'commentBody'"
      :text-area-error="errors.body"
      @removeError="handleRemoveError('body')"
    />

    <div className="field is-grouped">
      <div className="control">
        <button type="submit" className="button is-link">Save</button>
      </div>
      <div className="control">
        <button type="reset" className="button is-link is-light">Cancel</button>
      </div>
    </div>
  </form>

  <div class="block error-message" v-if="error">
    <p class="title is-6">{{ error }}</p>
  </div>
</template>
