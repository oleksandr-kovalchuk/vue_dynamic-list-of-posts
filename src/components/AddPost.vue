<script setup>
import { ref, watch } from 'vue'
import InputField from './InputField.vue'
import TextAreaField from './TextAreaField.vue'

const props = defineProps({
  selectedPost: {
    type: Object,
    default: () => ({}),
  },
})

const emit = defineEmits(['closeForm', 'createPost', 'updatePost'])

const fields = ref({
  title: '',
  body: '',
})

const errors = ref({
  title: '',
  body: '',
})

watch(
  () => props.selectedPost,
  (newPost) => {
    if (!newPost || !newPost.id) {
      fields.value.title = ''
      fields.value.body = ''
      return
    }

    fields.value.title = newPost.title
    fields.value.body = newPost.body
  },
  { deep: true, immediate: true },
)

const handleRemoveError = (key) => {
  errors.value[key] = ''
}

const handleCheckErrors = (key) => {
  if (!fields.value[key]) {
    errors.value[key] = `${key} is required!`
  }
}

const handleClose = () => {
  emit('closeForm')
}

const handleSubmit = () => {
  Object.keys(fields.value).forEach((key) => {
    handleCheckErrors(key)
  })

  if (Object.values(errors.value).some((error) => error.length > 0)) {
    return
  }

  if (Object.keys(props.selectedPost).includes('id')) {
    emit('updatePost', { ...props.selectedPost, ...fields.value })
    return
  }

  emit('createPost', { ...fields.value })
  fields.value.title = ''
  fields.value.body = ''
}
</script>

<template>
  <div className="content">
    <h2>{{ selectedPost.id !== undefined ? 'Editing Post' : 'Create new post' }}</h2>

    <form v-on:submit.prevent="handleSubmit" v-on:reset="handleClose">
      <InputField
        v-model="fields.title"
        :inputName="'postTitle'"
        :inputError="errors.title"
        @removeError="handleRemoveError('title')"
      />
      <TextAreaField
        v-model="fields.body"
        :textAreaName="'postBody'"
        :textAreaError="errors.body"
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
  </div>
</template>
