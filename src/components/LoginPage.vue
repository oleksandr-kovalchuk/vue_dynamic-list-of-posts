<script setup>
import { ref } from 'vue'
import { getUserByEmail, createUser } from '@/api/users'

const emit = defineEmits(['addUser'])

const email = ref('')
const name = ref('')
const errors = ref({
  email: '',
  name: '',
  total: '',
})
const isNeedRegister = ref(false)

const validateEmail = () => {
  if (!email.value.trim().length) {
    errors.value.email = 'Email is required'
    return
  }

  const emailRegex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/

  if (!emailRegex.test(email.value)) {
    errors.value.email = 'Email is not valid'
    return
  }
}

const validateName = () => {
  if (!name.value.trim().length) {
    errors.value.name = 'Name is required'
    return
  }

  if (name.value.trim().length < 2) {
    errors.value.name = 'Name must have at least 2 chars'
    return
  }
}

const handleLogin = () => {
  validateEmail()

  if (errors.value.email) {
    return
  }

  getUserByEmail(email.value.trim())
    .then(({ data }) => {
      if (!data.length) {
        isNeedRegister.value = true
        return
      }

      emit('addUser', data[0])
    })
    .catch((e) => {
      console.log(e)
      errors.value.total = 'Server Error. Try again later!'
    })
}

const handleRegister = () => {
  validateEmail()
  validateName()

  if (errors.value.email || errors.value.name) {
    return
  }

  const nameValue = name.value.trim()
  const emailValue = email.value.trim()
  const username = null
  const phone = null

  createUser({ name: nameValue, email: emailValue, username, phone })
    .then(({ data }) => {
      emit('addUser', data)
    })
    .catch((e) => {
      console.log(e)
      errors.value.total = 'Server Error. Try again later!'
    })
}
</script>

<template>
  <section class="container is-flex is-justify-content-center">
    <form
      class="box mt-5"
      @submit.prevent="isNeedRegister ? handleRegister() : handleLogin()"
      novalidate
    >
      <h1 class="title is-3">{{ isNeedRegister ? 'You need to register' : 'Get your userId' }}</h1>

      <div class="field">
        <label class="label" htmlFor="user-email"> Email </label>

        <div class="control has-icons-left" :class="{ 'has-icons-right': errors.email }">
          <input
            v-model="email"
            type="email"
            id="user-email"
            name="email"
            class="input"
            :class="{ 'is-danger': errors.email }"
            placeholder="Enter your email"
            @input="errors.email = ''"
          />

          <span class="icon is-small is-left">
            <i class="fas fa-envelope"></i>
          </span>

          <span class="icon is-small is-right has-text-danger" v-if="errors.email">
            <i class="fas fa-exclamation-triangle"></i>
          </span>
        </div>

        <p class="help is-danger" v-if="errors.email">{{ errors.email }}</p>
      </div>

      <div class="field" v-if="isNeedRegister">
        <label class="label" htmlFor="user-name"> Name </label>

        <div class="control has-icons-left" :class="{ 'has-icons-right': errors.name }">
          <input
            v-model="name"
            type="text"
            id="user-name"
            name="name"
            class="input"
            :class="{ 'is-danger': errors.name }"
            placeholder="Enter your name"
            @input="errors.name = ''"
          />

          <span class="icon is-small is-left">
            <i className="fas fa-user"></i>
          </span>

          <span class="icon is-small is-right has-text-danger" v-if="errors.name">
            <i class="fas fa-exclamation-triangle"></i>
          </span>
        </div>

        <p class="help is-danger" v-if="errors.name">{{ errors.name }}</p>
      </div>

      <div class="field">
        <button type="submit" class="button is-primary">
          {{ isNeedRegister ? 'Register' : 'Login' }}
        </button>
      </div>
      <p class="help is-danger" v-if="errors.total">{{ errors.total }}</p>
    </form>
  </section>
</template>
