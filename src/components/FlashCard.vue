<template>
  <div class="max-w-2xl mx-auto space-y-6">
    <RoundedCard>
      <h3 class="p-4 font-medium text-xl border-b border-gray-200">
        {{ currentWord.word }}
      </h3>

      <div class="p-4 space-y-4">
        <textarea
          v-model="userDefinition"
          class="w-full p-2 border rounded-md"
          placeholder="Type the definition..."
          :disabled="isChecking"
        ></textarea>

        <div class="flex justify-between">
          <button
            @click="checkDefinition"
            class="px-4 py-2 bg-blue-500 text-white rounded-md disabled:opacity-50"
            :disabled="isChecking || !userDefinition"
          >
            {{ isChecking ? "Checking..." : "Check" }}
          </button>

          <div class="space-x-2">
            <button
              @click="prevCard"
              class="px-4 py-2 border rounded-md"
              :disabled="currentIndex === 0"
            >
              Previous
            </button>
            <button
              @click="nextCard"
              class="px-4 py-2 border rounded-md"
              :disabled="currentIndex === words.length - 1"
            >
              Next
            </button>
          </div>
        </div>

        <div v-if="feedback" :class="['p-4 rounded-md', feedbackColor]">
          {{ feedback }}
        </div>
      </div>
    </RoundedCard>

    <div class="text-center text-gray-600">
      {{ currentIndex + 1 }} / {{ words.length }}
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import RoundedCard from "@/components/RoundedCard.vue";

const props = defineProps({
  words: {
    type: Array,
    required: true,
  },
});

const currentIndex = ref(0);
const userDefinition = ref("");
const isChecking = ref(false);
const feedback = ref("");
const feedbackColor = ref("");

const currentWord = computed(() => props.words[currentIndex.value]);

const checkDefinition = async () => {
  isChecking.value = true;
  feedback.value = "";

  try {
    const prompt = `Compare these two definitions for the word "${currentWord.value.word}":
    Reference: "${currentWord.value.definition}"
    My definition: "${userDefinition.value}"
    
    Is my definition correct? Keep the explanation at a 7th grade reading level. And be kind. My definition only has to be 
    generally close to the definition.
    Respond with:
    1. Yes/No
    2. Brief explanation why`;

    const response = await fetch(
      `${import.meta.env.VITE_API_BASE_URL}/chat/llama3`,
      {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "llama3",
          prompt: prompt,
        }),
      }
    );

    const result = await response.json();
    feedback.value = result.data.response;
    feedbackColor.value = result.data.response.toLowerCase().includes("yes")
      ? "bg-green-100"
      : "bg-red-100";
  } catch (error) {
    feedback.value = "Error checking definition. Please try again.";
    feedbackColor.value = "bg-red-100";
  } finally {
    isChecking.value = false;
  }
};

const nextCard = () => {
  if (currentIndex.value < props.words.length - 1) {
    currentIndex.value++;
    resetCard();
  }
};

const prevCard = () => {
  if (currentIndex.value > 0) {
    currentIndex.value--;
    resetCard();
  }
};

const resetCard = () => {
  userDefinition.value = "";
  feedback.value = "";
};
</script>
