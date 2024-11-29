<template>
  <SectionCard>
    <template #word>
      {{ word }}
    </template>
    <template #definition>
      {{ definition }}
    </template>
    <template #sentence>
      {{ sentence }}
    </template>
  </SectionCard>
</template>

<script setup>
import SectionCard from "@/components/SectionCard.vue";
defineProps({
  word: String,
  definition: String,
  sentence: String,
});
</script>
