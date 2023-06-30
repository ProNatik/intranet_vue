<script setup lang="ts">
import UserCard from '@/components/UserCard.vue'
import UserForm from '@/components/UserForm.vue'
import { useCollaborator } from '@/composables/collaborators'
import router from '@/router'
import { update } from '@/services/users'
import type { APIError, User } from '@/services/users.types'
import { useTitle } from '@vueuse/core'
import { ref } from 'vue'

useTitle('Modifie collaborateur - Vue Intranet')

const props = defineProps<{
  userId: string
}>()

const { user, isLoading, error } = useCollaborator(props.userId)

const errorMessage = ref<string | null>(null)

async function updateUser(userToUpdate: Partial<User>) {
  // 1. Appeler la méthode du UserService qui va modifier un utilisateur
  error.value = null
  try {
    await update(userToUpdate)
    router.push({ name: 'list' })
  } catch (error) {
    errorMessage.value = (error as APIError).message
  }
}
</script>

<template>
  <h1 class="text-h3 my-4">Modification d'un collaborateur</h1>

  <v-divider class="mb-8" />

  <!-- User Card -->
  <UserCard v-if="user" :user="user" class="mb-8" />
  <v-skeleton-loader
    v-else-if="isLoading"
    type="card"
    class="ma-auto"
    :style="{ maxWidth: '30rem' }"
  />
  <!-- Formulaire -->
  <UserForm v-if="user" @validate="updateUser" :user="user" />
  <v-row v-else-if="isLoading">
    <v-col cols="12" md="6" offset-md="3">
      <v-skeleton-loader v-for="n in 9" :key="n" type="heading" class="mb-6" />
      <v-skeleton-loader type="button" />
    </v-col>
  </v-row>
  <v-alert type="error" v-if="error" :text="error" />
  <v-alert type="error" v-if="errorMessage" :text="errorMessage" />
</template>

<style scoped>
h1 {
  text-align: center;
}
</style>
