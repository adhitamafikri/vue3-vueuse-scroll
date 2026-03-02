<template>
  <section>
    <h1>Mouse Movement View</h1>
    <p>Mouse position: X: {{ x }}, Y: {{ y }}</p>
  </section>
</template>

<script setup lang="ts">
import { useMouse } from "@vueuse/core";
const { x, y } = useMouse();
</script>
