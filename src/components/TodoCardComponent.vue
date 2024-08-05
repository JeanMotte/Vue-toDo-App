<template>
  <v-card class="mx-auto" hover max-width="900">
    <v-card-text>
      <v-row align="center">
        <v-col>
          <h3 class="text-xl" :class="{ 'line-through': isChecked }">{{ card.title }}</h3>
          <p :class="{ 'line-through': isChecked }">{{ card.subtitle }}</p>
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
            <v-btn class="me-3" icon @click="$emit('toggle-like', card.id)">
              <v-icon>{{ card.liked ? 'mdi-heart' : 'mdi-heart-outline' }}</v-icon>
            </v-btn>
            <v-btn icon @click="$emit('delete-card', card.id)">
              <v-icon size="30">mdi-delete</v-icon>
            </v-btn>
          </v-row>
        </v-col>
      </v-row>
    </v-card-text>
  </v-card>
</template>

<script setup lang="ts">
  import { defineProps, defineEmits, computed } from 'vue'

  const props = defineProps({
    card: {
      type: Object,
      required: true
    },
    checkedItems: {
      type: Array,
      required: true
    }
  })

  const emit = defineEmits(['toggle-like', 'delete-card'])

  const isChecked = computed(() => props.checkedItems.includes(props.card.id))
</script>
