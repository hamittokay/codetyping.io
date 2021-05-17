<template>
  <div class="min-h-screen flex items-center justify-center">
    <TypingArea />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import TypingArea from "@/components/typing-area.vue";

export default Vue.extend({
  components: {
    TypingArea
  }
});
</script>
