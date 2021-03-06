<template>
  <span class="word relative flex py-1">
    <span
      v-for="(letter, i) in letters"
      :key="i"
      class="opacity-40 text-gray-100"
      :class="{
        'opacity-100': isCorrect(letter, i),
        invalid: isError(letter, i),
        'border-0 border-b-2 opacity-60 bg-gray-600': isActive(i)
      }"
    >
      {{ letter }}
    </span>
    <!-- <span v-if="active" class="cursor" :style="{ left: `${cursorX}px` }" /> -->
  </span>
</template>

<script>
import Vue from "vue";
import { allowedKeys } from "~/config/key-codes";

export default Vue.extend({
  name: "word",

  data: () => ({
    typedWord: ""
  }),

  props: {
    word: {
      type: String,
      required: true
    },
    active: {
      type: Boolean,
      required: true
    },
    playgroundActive: {
      type: Boolean,
      required: true
    }
  },

  computed: {
    letters() {
      return this.word.split("");
    }
  },

  methods: {
    initListener() {
      this.keydownListener = window.addEventListener("keydown", event => {
        if (this.active && this.playgroundActive) {
          const { key } = event;
          if (allowedKeys.includes(key.toLowerCase())) {
            if (this.word.length > this.typedWord.length) {
              this.typedWord += key;
            }
          }

          if (key == "Backspace") {
            this.typedWord = this.typedWord.slice(0, -1);
          }

          this.$emit("keypress", {
            event,
            typedWord: this.typedWord
          });
        }
      });
    },

    destroyListener() {
      window.removeEventListener("keydown", this.keydownListener);
    },

    isCorrect(letter, i) {
      return this.typedWord.length > i && this.typedWord[i] === letter;
    },

    isError(letter, i) {
      return this.typedWord.length > i && this.typedWord[i] !== letter;
    },

    isActive(i) {
      return (
        this.active &&
        ((i === 0 && this.typedWord.length == 0) ||
          i === this.typedWord.length - 1)
      );
    }
  },

  mounted() {
    if (this.active) {
      this.initListener();
    }
  },

  watch: {
    active(value) {
      if (value && this.playgroundActive) {
        this.initListener();
      } else {
        this.destroyListener();
      }
    }
  }
});
</script>
