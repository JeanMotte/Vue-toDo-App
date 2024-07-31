<template>
  <v-card class="mx-auto" color="grey-lighten-3">
    <v-layout>
      <v-app-bar
        color="teal-darken-4"
        image="https://picsum.photos/1920/1080?random"
      >
        <template #image>
          <v-img
            gradient="to top right, rgba(19,84,122,.8), rgba(128,208,199,.8)"
          />
        </template>

        <template #prepend>
          <v-app-bar-nav-icon />
        </template>

        <v-app-bar-title>Title</v-app-bar-title>

        <v-spacer />

        <v-btn icon>
          <v-icon>mdi-magnify</v-icon>
        </v-btn>

        <v-btn icon>
          <v-icon>mdi-heart</v-icon>
        </v-btn>

        <v-btn icon>
          <v-icon>mdi-dots-vertical</v-icon>
        </v-btn>
      </v-app-bar>

      <v-main>
        <v-container fluid>
          <v-row dense>
            <v-col
              v-for="n in 6"
              :key="n"
              class="mt-3"
              cols="12"
            >
              <v-card
                class="mx-auto"
                hover
                max-width="900"
                :subtitle="`Subtitle for Content ${n}`"
                :title="`Content ${n}`"
              >
                <v-card-actions
                  class="justify-end"
                >
                  <v-btn><v-icon
                    icon="mdi-delete"
                    size="30"
                  /></v-btn>
                </v-card-actions>
              </v-card></v-col>
          </v-row>
        </v-container>
      </v-main>
    </v-layout>
  </v-card>
</template>

<script setup lang="ts">
</script>
