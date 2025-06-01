<script setup>
import { ref, watch, onMounted } from 'vue'

const props = defineProps({
  modelValue: String,
  inputName: String,
  inputError: String,
})

const emit = defineEmits(['update:modelValue', 'removeError'])

const label = ref('')
const type = ref('')
const placeholder = ref('')
const iconClass = ref('')
const internalValue = ref(props.modelValue)

onMounted(() => {
  switch (props.inputName) {
    case 'postTitle':
      label.value = 'Title'
      type.value = 'text'
      placeholder.value = 'Post title'
      iconClass.value = 'fa-user'
      break
    case 'commentAuthor':
      label.value = 'Author Name'
      type.value = 'text'
      placeholder.value = 'Name Surname'
      iconClass.value = 'fa-user'
      break
    case 'commentAuthorEmail':
      label.value = 'Author Email'
      type.value = 'email'
      placeholder.value = 'Your Email'
      iconClass.value = 'fa-envelope'
      break
    default:
      break
  }
})

watch(
  () => props.modelValue,
  (newVal) => {
    internalValue.value = newVal
  },
)

watch(internalValue, (newVal) => {
  emit('update:modelValue', newVal)
})
</script>

<template>
  <div class="field">
    <label class="label" :htmlFor="inputName">
      {{ label }}
    </label>
    <div class="control has-icons-left has-icons-right">
      <input
        :type="type"
        :name="inputName"
        :id="inputName"
        :placeholder="placeholder"
        class="input"
        :class="{ 'is-danger': inputError }"
        v-model="internalValue"
        @input="emit('removeError')"
      />
      <span class="icon is-small is-left">
        <i class="fas" :class="iconClass"></i>
      </span>

      <span class="icon is-small is-right has-text-danger" v-if="inputError">
        <i class="fas fa-exclamation-triangle"></i>
      </span>
    </div>

    <p class="help is-danger" v-if="inputError">{{ inputError }}</p>
  </div>
</template>
