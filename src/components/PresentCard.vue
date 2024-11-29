<template>
  <div
    class="min-h-screen bg-white z-50 flex flex-col justify-center items-center p-16"
  >
    <div class="bg-gray-100 rounded-lg shadow-xl w-full max-w-6xl p-12">
      <h2 class="text-6xl font-medium mb-8 border-b pb-6">Word: {{ word }}</h2>
      <div class="text-4xl space-y-12">
        <p class="mb-8">
          <span class="text-gray-700">Definition:</span> {{ definition }}
        </p>
        <p><span class="text-gray-700">Sentence:</span> {{ sentence }}</p>
      </div>
    </div>

    <div class="mt-12 space-x-4">
      <button
        @click="$emit('prev')"
        class="px-8 py-4 text-2xl bg-gray-100 rounded-lg hover:bg-gray-200"
      >
        Previous
      </button>
      <button
        @click="$emit('next')"
        class="px-8 py-4 text-2xl bg-gray-100 rounded-lg hover:bg-gray-200"
      >
        Next
      </button>
    </div>
  </div>
</template>

<script setup>
defineProps(["word", "definition", "sentence"]);
defineEmits(["prev", "next"]);
</script>
