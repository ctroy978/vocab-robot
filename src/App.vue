<template>
  <main class="container mx-auto my-8 space-y-8">
    <NavBar @viewChange="handleViewChange" @weekChange="handleWeekChange" />

    <!-- list words -->
    <section v-if="currentView === 'list'" class="grid grid-cols-2 gap-8">
      <WordCard
        v-for="word in currentWeekWords"
        :key="word.word"
        :word="word.word"
        :definition="word.definition"
        :sentence="word.sentence"
      />
    </section>

    <!-- presentation -->
    <section v-if="currentView === 'presentation'" class="h-screen">
      <PresentCard
        :word="currentWeekWords[currentWordIndex].word"
        :definition="currentWeekWords[currentWordIndex].definition"
        :sentence="currentWeekWords[currentWordIndex].sentence"
        @next="nextWord"
        @prev="prevWord"
      />
    </section>

    <!-- flash cards -->

    <section v-if="currentView === 'flashcard'" class="px-4">
      <FlashCard :words="currentWeekWords" />
    </section>

    <!-- vocab test -->
    <section v-if="currentView === 'test'" class="px-4">
      <VocabTest
        :current-week="currentWeek"
        :all-words="
          vocabData.weeks.flatMap((week) =>
            week.words.map((word) => ({ ...word, week: week.weekNumber }))
          )
        "
      />
    </section>
  </main>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import NavBar from "@/components/NavBar.vue";
import WordCard from "@/components/WordCard.vue";
import PresentCard from "@/components/PresentCard.vue";
import FlashCard from "@/components/FlashCard.vue";
import VocabTest from "@/components/VocabTest.vue";

//const vocabData = await import("@/assets/vocabulary.json");
let vocabData = ref(null);

onMounted(async () => {
  vocabData.value = await import("@/assets/vocabulary.json");
});

const currentWeek = ref(1);
const currentView = ref("list");
const currentWordIndex = ref(0);

const currentWeekWords = computed(() => {
  // Check if vocabData has been loaded before accessing its properties
  return vocabData.value && vocabData.value.weeks
    ? vocabData.value.weeks.find((w) => w.weekNumber === currentWeek.value)
        .words
    : [];
});

const handleViewChange = (newView) => {
  currentView.value = newView;
};

const handleWeekChange = (newWeek) => {
  // handle what happens when the week changes in the app
  currentWeek.value = newWeek;
};

const nextWord = () => {
  if (currentWordIndex.value < currentWeekWords.value.length - 1) {
    currentWordIndex.value++;
  }
};

const prevWord = () => {
  if (currentWordIndex.value > 0) {
    currentWordIndex.value--;
  }
};
</script>
