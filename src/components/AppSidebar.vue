<script setup>
import { ref, watch } from 'vue'
import { createComment, getCommentsByPostId, removeComment } from '@/api/comments'
import AddPost from './AddPost.vue'
import PostComment from './PostComment.vue'
import PostPreview from './PostPreview.vue'
import AppLoader from './AppLoader.vue'
import CommentForm from './CommentForm.vue'

const props = defineProps({
  isActive: Boolean,
  selectedPost: {
    type: Object,
    default: () => ({}),
  },
})

const emit = defineEmits(['deletePost', 'closeSidebar', 'createPost', 'updatePost'])

const hasUpdateForm = ref(false)
const comments = ref([])
const isLoadingComments = ref(false)
const isCommentForm = ref(false)
const errorMessage = ref('')

const handleCreateComment = (commentData) => {
  const postId = props.selectedPost.id
  createComment({ ...commentData, postId })
    .then(({ data }) => {
      comments.value.push(data)
      isCommentForm.value = false
    })
    .catch(() => {
      errorMessage.value = 'Something went wrong with creating comment!'
    })
}

const handleDeleteComment = (commentId) => {
  removeComment(commentId)
    .then(() => {
      comments.value = comments.value.filter((comment) => comment.id !== commentId)
    })
    .catch(() => {
      errorMessage.value = 'Something went wrong with deleting comment!'
    })
}

const handleCloseForm = () => {
  if (Object.keys(props.selectedPost).includes('id')) {
    hasUpdateForm.value = false
    return
  }

  emit('closeSidebar')
}

const handleCloseCommentForm = () => {
  isCommentForm.value = false
  errorMessage.value = ''
}

watch(
  () => props.selectedPost,
  (newPost) => {
    hasUpdateForm.value = false

    if (!newPost || !newPost.id) {
      comments.value = []
      return
    }

    isLoadingComments.value = true

    getCommentsByPostId(newPost.id)
      .then(({ data }) => {
        comments.value = data
      })
      .catch(() => {
        errorMessage.value = 'Something went wrong!'
      })
      .finally(() => {
        isLoadingComments.value = false
      })
  },
  { deep: true, immediate: true },
)
</script>

<template>
  <div class="tile is-parent Sidebar" :class="{ 'Sidebar--open': isActive }">
    <div class="tile is-child box is-success">
      <div class="tile is-child box is-success">
        <div class="content">
          <template v-if="selectedPost.hasOwnProperty('id') && hasUpdateForm === false">
            <PostPreview
              :selectedPost="selectedPost"
              @deletePost="emit('deletePost', $event)"
              @updatePost="hasUpdateForm = true"
            />

            <template v-if="!isCommentForm">
              <div
                class="is-flex is-justify-content-center is-align-items-center mt-2"
                v-if="isLoadingComments"
              >
                <AppLoader />
              </div>

              <div className="block" v-else-if="!isLoadingComments && comments.length === 0">
                <p className="title is-4">No comments yet</p>
              </div>

              <template v-else>
                <PostComment
                  v-for="comment of comments"
                  :key="comment.id"
                  :comment="comment"
                  @delete-comment="handleDeleteComment"
                />
              </template>

              <button type="button" className="button is-link" @click="isCommentForm = true">
                Write a comment
              </button>

              <div class="block error-message" v-if="!isLoadingComments && errorMessage">
                <p>{{ errorMessage }}</p>
              </div>
            </template>
            <CommentForm
              v-else
              @create-comment="handleCreateComment"
              @close-form="handleCloseCommentForm"
              :error="errorMessage"
            />
          </template>

          <AddPost
            v-else
            :selectedPost="selectedPost"
            @closeForm="handleCloseForm"
            @createPost="emit('createPost', $event)"
            @updatePost="emit('updatePost', $event)"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.Sidebar {
  overflow: hidden;
  opacity: 0;
  transition-property: max-width, opacity;
  transition-duration: 0.5s;
  transition-timing-function: ease-in-out;

  @media (min-width: 769px) {
    max-width: 0;
  }
}

.Sidebar--open {
  opacity: 1;

  @media (min-width: 769px) {
    max-width: 50%;
  }
}
</style>
