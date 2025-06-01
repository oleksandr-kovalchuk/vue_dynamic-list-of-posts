<script setup>
import { ref, onMounted } from 'vue'
import { getPostsByUserId, removePost, createPost, updatePost } from '@/api/posts'
import AppLoader from './AppLoader.vue'
import AppSidebar from './AppSidebar.vue'

const props = defineProps({
  userId: Number,
})

const posts = ref([])
const isLoading = ref(false)
const selectedPost = ref({})
const isSidebarActive = ref(false)

onMounted(() => {
  isLoading.value = true

  getPostsByUserId(props.userId)
    .then(({ data }) => {
      posts.value = data
    })
    .finally(() => {
      isLoading.value = false
    })
})

const handleAddPost = () => {
  isSidebarActive.value = true
  selectedPost.value = {}
}

const handleSelectPost = (post) => {
  isSidebarActive.value = true
  selectedPost.value = { ...post }
}

const handleCloseSidebar = () => {
  selectedPost.value = {}
  isSidebarActive.value = false
}

const handleDeletePost = (postId) => {
  removePost(postId).then(() => {
    posts.value = posts.value.filter((post) => post.id !== postId)
    isSidebarActive.value = false
  })
}

const handleCreatePost = (newPost) => {
  const userId = props.userId
  createPost({ ...newPost, userId }).then(({ data }) => {
    posts.value.push(data)
    selectedPost.value = posts.value[posts.value.length - 1]
  })
}

const handleUpdatePost = (postToUpdate) => {
  const userId = props.userId
  updatePost(postToUpdate.id, { ...postToUpdate, userId }).then(({ data }) => {
    posts.value = posts.value.map((post) => {
      if (post.id === data.id) {
        return data
      }
      return post
    })
    selectedPost.value = posts.value.find((post) => post.id === data.id)
  })
}
</script>

<template>
  <div class="tile is-parent is-flex-grow-1">
    <div class="tile is-child box is-success">
      <div class="block">
        <div class="block is-flex is-justify-content-space-between">
          <p class="title">Posts</p>
          <button type="button" class="button is-link" @click="handleAddPost">Add New Post</button>
        </div>

        <!-- Loader -->
        <div class="is-flex is-justify-content-center is-align-items-center mt-2" v-if="isLoading">
          <AppLoader />
        </div>

        <p
          class="is-flex is-justify-content-center is-align-items-center mt-2"
          v-else-if="!isLoading && posts.length === 0"
        >
          No posts yet
        </p>

        <!-- Posts Table -->
        <table class="table is-fullwidth is-striped is-hoverable is-narrow" v-else>
          <thead>
            <tr class="has-background-link-light">
              <th>ID</th>
              <th>Title</th>
              <th class="has-text-right">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="post of posts" :key="post.id">
              <td>{{ post.id }}</td>
              <td>{{ post.title }}</td>
              <td class="has-text-right is-vcentered">
                <button
                  type="button"
                  class="button is-link"
                  :class="{ 'is-light': post.id !== selectedPost.id }"
                  @click="
                    post.id === selectedPost.id ? handleCloseSidebar() : handleSelectPost(post)
                  "
                >
                  {{ post.id !== selectedPost.id ? 'Open' : 'Close' }}
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>

  <AppSidebar
    class="is-flex-grow-1"
    :isActive="isSidebarActive"
    :selectedPost="selectedPost"
    @deletePost="handleDeletePost"
    @createPost="handleCreatePost"
    @updatePost="handleUpdatePost"
    @closeSidebar="handleCloseSidebar"
  />
</template>
