<template>
  <div v-html="iconSvg" role="img" :aria-label="ariaLabel" />
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue";

const props = defineProps({
  name: {
    type: String,
    required: true,
  },
  ariaLabel: {
    type: String,
    default: "",
  },
});

const iconSvg = ref("");

onMounted(async () => {
  try {
    const svg = await import(`~/assets/icons/${props.name}.svg?raw`);
    iconSvg.value = svg.default;
  } catch (error) {
    console.error("Error loading SVG:", error);
  }
});
</script>
