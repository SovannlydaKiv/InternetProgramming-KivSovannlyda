<script setup lang="ts">
import { ref, computed } from 'vue';
import EventCard from './EventCard.vue';
import type { CampusEvent } from '../types';

const props = defineProps<{
  events: CampusEvent[]
}>();

const emit = defineEmits<{
  (e: 'book', id: number): void
}>();

const search = ref('');
const category = ref('all'); // Removed the strict type so it accepts any mock test category

// Dynamically grab all unique categories from the events list
const uniqueCategories = computed(() => {
  const allCats = props.events.map(e => e.category);
  return [...new Set(allCats)];
});

const visible = computed(() => {
  return props.events.filter(e => {
    const matchCategory = category.value === 'all' || e.category === category.value;
    const searchTerm = search.value.toLowerCase();
    const matchSearch = e.title.toLowerCase().includes(searchTerm) || 
                        e.location.toLowerCase().includes(searchTerm);
    return matchCategory && matchSearch;
  });
});
</script>

<template>
  <div class="event-list">
    <div class="controls">
      <input v-model="search" placeholder="Search title or location" />
      <select v-model="category">
        <option value="all">All</option>
        <!-- Automatically generate options based on the available data -->
        <option v-for="cat in uniqueCategories" :key="cat" :value="cat">
          {{ cat }}
        </option>
      </select>
    </div>

    <p>{{ visible.length }} of {{ events.length }} events</p>

    <div v-if="visible.length > 0" class="events-grid">
      <EventCard
        v-for="e in visible"
        :key="e.id"
        :event="e"
        @book="(id) => emit('book', id)"
      />
    </div>
    <div v-else class="empty-message">
      No events match your search.
    </div>
  </div>
</template>

<style scoped>
.filters { display: flex; gap: 8px; flex-wrap: wrap; }
.filters input { flex: 1; min-width: 200px; }
.count { color: #6b7280; }
.grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(260px, 1fr)); gap: 16px; }
.empty { padding: 32px; text-align: center; background: white; border-radius: 10px; }
</style>
