<template>
  <span class="word relative flex py-1">
    <span
      v-for="(letter, i) in letters"
      :key="i"
      class="opacity-60 text-gray-100"
      :class="{
        'opacity-100': isCorrect(letter, i),
        invalid: isError(letter, i)
      }"
    >
      {{ letter }}
    </span>
    <span v-if="active" class="cursor" :style="{ left: `${cursorX}px` }" />
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
    }
  },

  computed: {
    letters() {
      return this.word.split("");
    },
    cursorX() {
      return this.typedWord.length * 11.8;
    }
  },

  methods: {
    initListener() {
      this.keydownListener = window.addEventListener("keydown", event => {
        if (this.active) {
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
    }
  },

  mounted() {
    if (this.active) {
      this.initListener();
    }
  },

  watch: {
    active(value) {
      if (value) {
        this.initListener();
      } else {
        this.destroyListener();
      }
    }
  }
});
</script>
