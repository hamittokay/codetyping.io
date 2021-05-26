<template>
  <div class="max-w-7xl mx-auto px-2 sm:px-6 lg:px-8 mt-10">
    <SnippetOwner />
    <TypingArea />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import TypingArea from "@/components/typing-area.vue";
import SnippetOwner from "@/components/snippet-owner.vue";

export default Vue.extend({
  components: {
    TypingArea,
    SnippetOwner
  }
});
</script>
