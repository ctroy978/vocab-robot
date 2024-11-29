<template>
  <RoundedCard>
    <h3
      v-if="$slots.word"
      class="p-4 font-medium text-xl border-b border-gray-200"
    >
      Word: <slot name="word"></slot>
    </h3>
    <div v-if="$slots.definition" class="p-4 border-b border-gray-200">
      Definition: <slot name="definition"></slot>
    </div>
    <div v-if="$slots.sentence" class="p-4">
      Sentence: <slot name="sentence"></slot>
    </div>
  </RoundedCard>
</template>

<script setup>
import RoundedCard from "@/components/RoundedCard.vue";
</script>
