<script setup>
import { ref, watch, onMounted } from 'vue'

const props = defineProps({
  modelValue: String,
  textAreaName: String,
  textAreaError: String,
})

const emit = defineEmits(['update:modelValue', 'removeError'])

const label = ref('')
const placeholder = ref('')
const internalValue = ref(props.modelValue)

onMounted(() => {
  switch (props.textAreaName) {
    case 'postBody':
      label.value = 'Write Post Body'
      placeholder.value = 'Post body'
      break
    case 'commentBody':
      label.value = 'Write Comment'
      placeholder.value = 'Comment'
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
    <label class="label" :htmlFor="textAreaName">{{ label }} </label>
    <div class="control">
      <textarea
        :id="textAreaName"
        :name="textAreaName"
        :placeholder="placeholder"
        class="textarea"
        :class="textAreaError"
        v-model="internalValue"
        @input="emit('removeError')"
      ></textarea>
    </div>

    <p class="help is-danger" v-if="textAreaError">{{ textAreaError }}</p>
  </div>
</template>
