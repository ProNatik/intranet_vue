<script setup lang="ts">
import UserForm from '@/components/UserForm.vue'
import router from '@/router'
import { create } from '@/services/users'
import type { APIError, User } from '@/services/users.types'
import { ref } from 'vue'

const errorMessage = ref<string | null>(null)

async function addUser(user: Partial<User>) {
  // 1. Appeler la méthode du UserService qui va créer un utilisateur
  errorMessage.value = null
  try {
    await create(user)
    router.push({ name: 'list' })
  } catch (error) {
    errorMessage.value = (error as APIError).message
  }
}
</script>
<template>
  <h1 class="text-h3 my-4">Création d'un collaborateur</h1>

  <v-divider class="mb-8" />

  <!-- Formulaire-->
  <UserForm @validate="addUser" />
  <v-alert type="error" v-if="errorMessage" :text="errorMessage" />
</template>

<style scoped>
h1 {
  text-align: center;
}
</style>
