<script setup>
const props = defineProps({
  selectedPost: Object,
})

const emit = defineEmits(['deletePost', 'updatePost'])

const handleDeletePost = () => {
  emit('deletePost', props.selectedPost.id)
}

const handleUpdatePost = () => {
  emit('updatePost')
}
</script>

<template>
  <div class="block">
    <div class="is-flex is-justify-content-space-between is-align-items-center">
      <h2>#post {{ selectedPost.id }}: {{ selectedPost.title }}</h2>
      <div class="is-flex">
        <span class="icon is-small is-right is-clickable" @click="handleUpdatePost">
          <i class="fas fa-pen-to-square"></i>
        </span>
        <span
          class="icon is-small is-right has-text-danger is-clickable ml-3"
          @click="handleDeletePost"
        >
          <i class="fas fa-trash"></i>
        </span>
      </div>
    </div>
    <p data-cy="PostBody">{{ selectedPost.body }}</p>
  </div>
</template>
