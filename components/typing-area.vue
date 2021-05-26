<template>
  <div
    class="typing-area relative flex-1 max-w-3xl my-6 mx-auto bg-gray-900 rounded-lg p-5 shadow-xl overflow-x-scroll"
  >
    <div class="flex" v-for="(line, i) in lines" :key="i">
      <Word
        v-for="(word, j) in words(line)"
        :word="word"
        :active="activeWordIdx === j && activeLineIdx == i"
        :key="j"
        @keypress="onKeypress"
      />
      <img
        v-show="activeLineIdx == i"
        class="w-3 -mt-1"
        src="/icons/enter.svg"
        alt="Enter"
      />
    </div>
  </div>
</template>

<script>
import Vue from "vue";

export default Vue.extend({
  name: "typing-area",

  data: () => ({
    code:
      "/** Report a error in process for error collection. */\n  public report (error: Error | ListrError): void {\n    /* istanbul ignore if */\n    if (error instanceof ListrError) {\n      for (const err of error.errors) {\n        this.errors.push(err)\n        this.task.message$ = { error: err.message || this.task?.title || 'Task with no title.' }\n      }\n    } else {\n      this.errors.push(error)\n      this.task.message$ = { error: error.message || this.task?.title || 'Task with no title.' }\n    }\n  }",
    activeWordIdx: 0,
    activeLineIdx: 0,
    activeTypedWord: ""
  }),

  computed: {
    lines() {
      return this.code.split("\n");
    },
    activeWord() {
      return this.words(this.activeLine)[this.activeWordIdx] || null;
    },
    activeLine() {
      return this.lines[this.activeLineIdx];
    },
    isActiveWordLastWordOfActiveLine() {
      return this.words(this.activeLine).length - 1 === this.activeWordIdx;
    }
  },

  methods: {
    words(line) {
      return line && typeof line === "string" ? line.split(" ") : [];
    },

    onKeypress({ event, typedWord }) {
      if (this.isActiveWordLastWordOfActiveLine && event.key !== "Enter") {
        return;
      }

      switch (event.key) {
        case " ":
          event.preventDefault();
          if (typedWord.length >= this.activeWord.length) {
            this.activeWordIdx++;
            this.activeTypedWord = "";
          }
          break;

        case "Enter":
          if (!this.isActiveWordLastWordOfActiveLine) {
            return;
          }

          this.activeWordIdx = 0;
          this.activeLineIdx++;
          this.activeTypedWord = "";

          if (this.words(this.activeLine).some(_ => _)) {
            while (!this.activeWord) {
              this.activeWordIdx++;
            }
          } else {
            while (!this.words(this.activeLine).some(_ => _)) {
              this.activeLineIdx++;
            }
          }
          break;

        default:
          this.activeTypedWord = typedWord;
          break;
      }
    }
  }
});
</script>
