<template>
  <v-card class="mx-auto" color="grey-lighten-3" height="100%">
    <v-layout>
      <v-app-bar color="teal-darken-4" image="https://picsum.photos/1920/1080?random">
        <template #image>
          <v-img gradient="to top right, rgba(19,84,122,.8), rgba(128,208,199,.8)" />
        </template>
        <template #prepend>
          <v-app-bar-nav-icon />
        </template>
        <v-app-bar-title>My toDo App</v-app-bar-title>
        <v-spacer />

        <v-text-field
          v-model="searchQuery"
          append-icon="mdi-magnify"
          class="search-input"
          hide-details
          label="Search todos"
          single-line
        />

        <v-btn icon @click="toggleFilterLike">
          <v-icon>{{ isFilterLiked ? 'mdi-heart' : 'mdi-heart-outline' }}</v-icon>
        </v-btn>
        <v-btn icon>
          <v-icon>mdi-dots-vertical</v-icon>
        </v-btn>
      </v-app-bar>

      <v-main class="mx-auto" max-width="900">
        <v-container fluid>
          <!-- Button Row -->
          <v-row class="flex justify-space-between mb-3" dense>
            <v-col cols="4">
              <v-btn block @click="setFilter('all')">All</v-btn>
            </v-col>
            <v-col cols="4">
              <v-btn block @click="setFilter('ongoing')">Ongoing</v-btn>
            </v-col>
            <v-col cols="4">
              <v-btn block color="warning" @click="clearAllCards">Clear all</v-btn>
            </v-col>
          </v-row>

          <!-- Input Row -->
          <v-row class="flex align-center" dense>
            <v-col>
              <v-text-field
                v-model="newCardInput"
                autofocus
                class="mr-2"
                hide-details="auto"
                label="Your toDo (e.g., Title | Subtitle)"
                @keyup.enter="addCard"
              />
            </v-col>
            <v-col cols="auto">
              <v-btn class="add-btn" color="primary" @click="addCard">Add</v-btn>
            </v-col>
          </v-row>

          <!-- No Liked Todos Message -->
          <v-row v-if="isFilterLiked && !displayedCards.length" dense>
            <v-col>
              <p class="text-center mt-5 text-xl text-gray-500 italic">No liked todos for the moment</p>
            </v-col>
          </v-row>

          <!-- Cards Display -->
          <v-row v-else class="flex" dense>
            <v-col v-for="card in displayedCards" :key="card.id" class="mt-3" cols="12">
              <v-card class="mx-auto" hover max-width="900">
                <v-card-text>
                  <v-row align="center">
                    <v-col>
                      <h3 class="text-xl" :class="{ 'line-through': checkedItems.includes(card.id) }">{{ card.title }}</h3>
                      <p :class="{ 'line-through': checkedItems.includes(card.id) }">{{ card.subtitle }}</p>
                    </v-col>
                    <v-col class="text-right">
                      <v-row align="center" justify="end">
                        <v-checkbox
                          v-model="checkedItems"
                          class="me-3"
                          color="success"
                          hide-details
                          :value="card.id"
                        />
                        <v-btn class="me-3" icon @click="toggleLike(card.id)">
                          <v-icon>{{ card.liked ? 'mdi-heart' : 'mdi-heart-outline' }}</v-icon>
                        </v-btn>
                        <v-btn icon @click="deleteCard(card.id)">
                          <v-icon size="30">mdi-delete</v-icon>
                        </v-btn>
                      </v-row>
                    </v-col>
                  </v-row>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-main>
    </v-layout>
  </v-card>
</template>

<script setup lang="ts">
  import { computed, onMounted, onUnmounted, ref } from 'vue'

  const checkedItems = ref<number[]>([])
  const newCardInput = ref('')
  const cards = ref<{ id: number; title: string; subtitle: string; liked: boolean }[]>([])
  let idCounter = 1

  // State to track the current filter and whether liked filter is applied
  const currentFilter = ref('all')
  const isFilterLiked = ref(false)

  // State for search query
  const searchQuery = ref('')

  // Add a new card
  const addCard = () => {
    if (newCardInput.value.trim()) {
      const [title, subtitle] = newCardInput.value.split('|').map(s => s.trim())
      cards.value.push({ id: idCounter++, title, subtitle: subtitle || '', liked: false })
      newCardInput.value = ''
    }
  }

  // Toggle the liked state of a card
  const toggleLike = (cardId: number) => {
    const card = cards.value.find(c => c.id === cardId)
    if (card) {
      card.liked = !card.liked
    }
  }

  // Set the current filter
  const setFilter = (filter: string) => {
    currentFilter.value = filter
  }

  // Delete a card
  const deleteCard = (cardId: number) => {
    cards.value = cards.value.filter(card => card.id !== cardId)
  }

  // Toggle the like filter
  const toggleFilterLike = () => {
    isFilterLiked.value = !isFilterLiked.value
  }

  // Computed property to determine which cards to display based on filter and search query
  const displayedCards = computed(() => {
    let filtered = cards.value

    if (isFilterLiked.value) {
      filtered = filtered.filter(card => card.liked)
    }

    if (currentFilter.value === 'ongoing') {
      filtered = filtered.filter(card => !checkedItems.value.includes(card.id))
    }

    // Filter by search query
    if (searchQuery.value) {
      const query = searchQuery.value.toLowerCase()
      filtered = filtered.filter(card => card.title.toLowerCase().includes(query) || card.subtitle.toLowerCase().includes(query))
    }

    return filtered
  })

  const clearAllCards = () => {
    return cards.value = []
  }

  // Handle the Esc key press
  const handleKeydown = (event: KeyboardEvent) => {
    if (event.key === 'Escape') {
      // Remove focus from the input
      (document.activeElement as HTMLElement)?.blur()
    }
  }

  // Add and remove event listeners
  onMounted(() => {
    window.addEventListener('keydown', handleKeydown)
  })

  onUnmounted(() => {
    window.removeEventListener('keydown', handleKeydown)
  })
</script>

<style scoped>
.line-through {
  text-decoration: line-through;
  color: grey;
}
.add-btn {
  min-height: 56px; /* Same height as the default v-text-field */
}
.search-input {
  max-width: 300px;
}
</style>
