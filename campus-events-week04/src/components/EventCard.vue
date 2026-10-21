<script setup lang="ts">
import { computed } from 'vue'
import type { CampusEvent } from '../types'
import { formatDate } from '../format'

const props = defineProps<{
  event: CampusEvent
}>()

const emit = defineEmits<{
  (e: 'book', id: number): void
}>()

const left = computed(() => props.event.capacity - props.event.booked)
const isFull = computed(() => left.value <= 0)
const almostFull = computed(() => left.value >= 1 && left.value <= 5)
</script>

<template>
  <!-- Must be <article> for App.test.ts -->
  <article class="event-card" :class="{ full: isFull }">
    <span class="category">{{ event.category }}</span>
    <h2>{{ event.title }}</h2>
    <p class="date">{{ formatDate(event.startsAt) }}</p>
    <p class="location">{{ event.location }}</p>

    <div class="seats">
      <span v-if="isFull" class="badge badge-full">Full</span>
      <span v-else :class="{ warn: almostFull }">
        {{ left === 1 ? '1 seat left' : `${left} seats left` }}
      </span>
    </div>

    <button 
      :disabled="isFull" 
      @click="emit('book', event.id)"
    >
      Book
    </button>
  </article>
</template>

<style scoped>
.card { background: white; border-radius: 10px; padding: 16px; display: flex; flex-direction: column; gap: 6px; }
.card.full { opacity: 0.7; }
h2 { font-size: 1.1rem; margin: 0; }
.category { text-transform: uppercase; font-size: 0.75rem; letter-spacing: 0.05em; color: #3178c6; margin: 0; }
.meta { color: #6b7280; margin: 0; }
.seats, .badge { margin: 0; font-weight: 600; }
.warn { color: #d73a49; }
.badge { color: white; background: #d73a49; align-self: flex-start; padding: 2px 10px; border-radius: 999px; }
button { margin-top: auto; align-self: flex-start; }
</style>
