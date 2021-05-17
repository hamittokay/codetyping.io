<template>
  <div
    class="typing-area relative flex-1 max-w-3xl mx-auto bg-gray-900 rounded-lg p-5 shadow-xl"
  >
    <div class="flex text-xl" v-for="(line, i) in lines" :key="i">
      <Word
        v-for="(word, j) in words(line)"
        :word="word"
        :active="activeWordIdx === j && activeLineIdx == i"
        :key="j"
        @keypress="onKeypress"
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
      "func (c *Color) Print(a ...interface{}) (n int, err error) {\n\tc.Set()\n\tdefer c.unset()\n\n\treturn fmt.Fprint(Output, a...)\n}",
    activeWordIdx: 0,
    activeLineIdx: 0,
    activeTypedWord: ""
  }),

  computed: {
    lines() {
      return this.code.split("\n").map(_ => _.trim());
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
