<template>
  <div class="max-w-4xl mx-auto space-y-6">
    <div class="mb-8">
      <h2 class="text-2xl font-bold mb-2">
        Vocabulary Test - Week {{ currentWeek }}
      </h2>
      <p class="text-gray-600">
        Define each word based on the example sentence provided. This test
        includes words from week {{ currentWeek }} and review words from
        previous weeks.
      </p>
    </div>

    <div v-if="!testStarted" class="text-center">
      <button
        @click="startTest"
        class="px-6 py-3 bg-blue-500 text-white rounded-md hover:bg-blue-600"
      >
        Start Test
      </button>
    </div>

    <form v-else @submit.prevent="submitTest" class="space-y-8">
      <div v-for="(word, index) in testWords" :key="index" class="space-y-4">
        <RoundedCard>
          <div class="p-4 space-y-4">
            <h3 class="font-medium text-xl">{{ word.word }}</h3>

            <div class="bg-gray-50 p-3 rounded-md">
              <p class="text-gray-700">Example: {{ word.sentence }}</p>
            </div>

            <div>
              <label
                :for="'definition-' + index"
                class="block text-sm font-medium text-gray-700 mb-2"
              >
                Your Definition:
              </label>
              <textarea
                :id="'definition-' + index"
                v-model="userDefinitions[index]"
                maxlength="200"
                class="w-full p-2 border rounded-md"
                :disabled="testSubmitted"
                rows="2"
                required
              ></textarea>
            </div>
          </div>
        </RoundedCard>
      </div>

      <div class="sticky bottom-4 bg-white p-4 border rounded-md shadow-lg">
        <div v-if="testSubmitted" class="text-center mb-4">
          <div class="p-4 bg-gray-50 rounded-md">
            {{ testFeedback }}
          </div>
        </div>

        <div class="flex justify-center space-x-4">
          <button
            v-if="!testSubmitted"
            type="submit"
            class="px-6 py-3 bg-blue-500 text-white rounded-md hover:bg-blue-600 disabled:opacity-50"
            :disabled="isSubmitting"
          >
            {{ isSubmitting ? "Grading..." : "Submit Test" }}
          </button>
          <button
            v-else
            type="button"
            @click="resetTest"
            class="px-6 py-3 bg-gray-500 text-white rounded-md hover:bg-gray-600"
          >
            Take Another Test
          </button>
        </div>
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref } from "vue";
import RoundedCard from "@/components/RoundedCard.vue";

const props = defineProps({
  currentWeek: {
    type: Number,
    required: true,
  },
  allWords: {
    type: Array,
    required: true,
  },
});

const testStarted = ref(false);
const testSubmitted = ref(false);
const isSubmitting = ref(false);
const userDefinitions = ref([]);
const testWords = ref([]);
const testFeedback = ref("");

const generateTestWords = () => {
  // Get current week's words
  const currentWeekWords = props.allWords.filter(
    (word) => word.week === props.currentWeek
  );

  // Get all words from previous weeks
  const previousWords = props.allWords.filter(
    (word) => word.week < props.currentWeek
  );

  // Randomly select 4-10 words from previous weeks
  const numReviewWords = Math.floor(Math.random() * 7) + 4;
  const shuffledPreviousWords = [...previousWords].sort(
    () => Math.random() - 0.5
  );
  const selectedReviewWords = shuffledPreviousWords.slice(0, numReviewWords);

  // Combine and shuffle all test words
  testWords.value = [...currentWeekWords, ...selectedReviewWords].sort(
    () => Math.random() - 0.5
  );

  // Initialize userDefinitions array
  userDefinitions.value = new Array(testWords.value.length).fill("");
};

const startTest = () => {
  generateTestWords();
  testStarted.value = true;
};

const submitTest = async () => {
  isSubmitting.value = true;

  try {
    // Create pairs of words with their reference and student definitions
    const wordPairs = testWords.value.map((word, index) => ({
      word: word.word,
      referenceDefinition: word.definition,
      studentDefinition: userDefinitions.value[index],
    }));

    // Create the test evaluation prompt
    const prompt = `You are grading a vocabulary test. Each correct definition is worth 1 point.
      A student's definition is correct if it shows a basic understanding of the word's meaning.
      They don't need to match exactly - just demonstrate comprehension of the core concept.

      Here are the word pairs to evaluate:
      ${wordPairs
        .map(
          (pair) => `
      Word: ${pair.word}
      Reference Definition: ${pair.referenceDefinition}
      Student Definition: ${pair.studentDefinition}
      `
        )
        .join("\n")}

      Please evaluate all definitions and provide:
      1. ALWAYS begin with a total score out of ${wordPairs.length} points
      2. A simple list of the words with incorrect definitions
      3. Brief, encouraging feedback about the student's overall performance`;

    const response = await fetch(
      `${import.meta.env.VITE_API_BASE_URL}/chat/llama3`,
      {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "llama3",
          prompt,
        }),
      }
    );

    const result = await response.json();
    testFeedback.value = result.data.response;
  } catch (error) {
    console.error("Error evaluating test:", error);
    testFeedback.value =
      "There was an error evaluating your test. Please try again.";
  } finally {
    isSubmitting.value = false;
    testSubmitted.value = true;
  }
};

const resetTest = () => {
  testStarted.value = false;
  testSubmitted.value = false;
  userDefinitions.value = [];
  testWords.value = [];
  testFeedback.value = "";
};
</script>
