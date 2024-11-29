<template>
  <nav class="bg-white shadow-sm px-6 py-4">
    <div class="flex justify-between items-center max-w-7xl mx-auto">
      <div class="flex items-center space-x-6">
        <div class="text-xl font-bold text-gray-800">Vocab</div>
        <div class="flex items-center space-x-2">
          <label class="text-gray-600">Week</label>
          <input
            type="number"
            min="1"
            max="30"
            v-model="currentWeek"
            class="w-16 px-2 py-1 border border-gray-300 rounded-md text-center focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
          />
          <span class="text-gray-600">/ 30</span>
        </div>
      </div>

      <div class="space-x-5">
        <button
          @click="handleViewChange('list')"
          class="px-4 py-2 rounded-md transition-colors"
          :class="
            currentView === 'list'
              ? 'bg-blue-500 text-white'
              : 'bg-gray-100 hover:bg-gray-200'
          "
        >
          List Words
        </button>
        <button
          @click="handleViewChange('presentation')"
          class="px-4 py-2 rounded-md transition-colors"
          :class="
            currentView === 'presentation'
              ? 'bg-blue-500 text-white'
              : 'bg-gray-100 hover:bg-gray-200'
          "
        >
          Presentation
        </button>
        <button
          @click="handleViewChange('flashcard')"
          class="px-4 py-2 rounded-md transition-colors"
          :class="
            currentView === 'flashcard'
              ? 'bg-blue-500 text-white'
              : 'bg-gray-100 hover:bg-gray-200'
          "
        >
          FlashCard
        </button>
        <button
          @click="handleViewChange('test')"
          class="px-4 py-2 rounded-md transition-colors"
          :class="
            currentView === 'test'
              ? 'bg-blue-500 text-white'
              : 'bg-gray-100 hover:bg-gray-200'
          "
        >
          Test
        </button>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { ref, watch } from "vue";

const currentWeek = ref(1);
const currentView = ref("list");

const emit = defineEmits(["viewChange", "weekChange"]); // Define the emit

const handleViewChange = (view) => {
  currentView.value = view;
  emit("viewChange", view); // Emit the event
};

watch(currentWeek, (newValue) => {
  // Ensure the week number is within bounds before emitting
  const validWeek = Math.min(30, Math.max(1, parseInt(newValue, 10)));

  currentWeek.value = validWeek;
  emit("weekChange", validWeek);
});
</script>
