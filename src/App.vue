<script setup>
import { onMounted, ref } from 'vue'

const tasks = ref([])
const title = ref('')

async function api(method, data) {
  const response = await fetch('/api/tasks.php', {
    method,
    headers: { 'Content-Type': 'application/json' },
    body: data && JSON.stringify(data),
  })
  tasks.value = await response.json()
}

onMounted(() => api('GET'))

async function add() {
  await api('POST', { title: title.value })
  title.value = ''
}
</script>

<template>
  <main>
    <h1>Mes tâches</h1>
    <p>Mise à jour déployée avec Git et cPanel.</p>

    <form @submit.prevent="add">
      <input v-model="title" placeholder="Nouvelle tâche" required maxlength="255" />
      <button>Ajouter</button>
    </form>

    <ul>
      <li v-for="task in tasks" :key="task.id" :class="{ done: Number(task.done) }">
        <button class="gris" @click="api('PATCH', { ...task, done: Number(task.done) ? 0 : 1 })">
          {{ Number(task.done) ? '✓' : '○' }}
        </button>
        <input v-model="task.title" required maxlength="255" />
        <button class="gris" @click="api('PATCH', task)">Modifier</button>
        <button class="gris" @click="api('DELETE', { id: task.id })">Supprimer</button>
      </li>
    </ul>
  </main>
</template>
