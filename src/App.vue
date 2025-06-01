<script setup>
import { ref, onMounted } from 'vue'
import AppHeader from './components/AppHeader.vue'
import LoginPage from './components/LoginPage.vue'
import PostsList from './components/PostsList.vue'

const user = ref({})

onMounted(() => {
  const userInLocalStorage = JSON.parse(localStorage.getItem('user'))
  if (userInLocalStorage) {
    user.value = userInLocalStorage
  }
})

const handleSaveUser = (userData) => {
  localStorage.setItem('user', JSON.stringify(userData))
  user.value = userData
}

const handleRemoveUser = () => {
  localStorage.removeItem('user')
  user.value = {}
}
</script>

<template>
  <LoginPage v-if="!user.hasOwnProperty('id')" @addUser="handleSaveUser" />

  <template v-else>
    <AppHeader :user="user" @log-out="handleRemoveUser" />
    <main class="section">
      <div class="container">
        <div class="tile is-ancestor is-flex is-flex-wrap-wrap">
          <PostsList :user-id="user.id" />
        </div>
      </div>
    </main>
  </template>
</template>

<style></style>
