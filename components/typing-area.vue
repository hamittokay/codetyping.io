<template>
  <div
    class="typing-area relative flex-1 my-6 mx-auto bg-gray-900 rounded-lg p-5 shadow-xl"
    id="typing-area"
    :class="{
      'overflow-x-scroll': playgroundActive,
      'overflow-x-hidden': !playgroundActive
    }"
  >
    <div
      class="absolute inset-0 w-full h-full bg-opacity-80 bg-gray-900 z-20 hover:text-white text-gray-600 flex items-center justify-center cursor-pointer"
      role="button"
      id="playground-activator"
      v-if="!playgroundActive"
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        width="60"
        height="60"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
        class="feather feather-play"
      >
        <polygon points="5 3 19 12 5 21 5 3"></polygon>
      </svg>
    </div>

    <div class="flex h-8" v-for="(line, i) in lines" :key="i">
      <span
        class=" mr-3 -ml-2 mt-1"
        :class="{
          'text-white': activeLineIdx == i,
          'text-gray-500': activeLineIdx !== i
        }"
        >{{ i + 1 }}</span
      >
      <Word
        v-for="(word, j) in words(line)"
        :word="word"
        :active="activeWordIdx === j && activeLineIdx == i"
        :key="j"
        @keypress="onKeypress"
        :playgroundActive="playgroundActive"
      />
      <img
        v-show="activeLineIdx == i"
        class="w-2 -mt-1"
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
      "fun! gotest#write_file(path, contents) abort\n  let l:dir = go#util#tempdir(\"vim-go-test/testrun/\")\n  let $GOPATH .= ':' . l:dir\n  let l:full_path = l:dir . '/src/' . a:path\n\n  call mkdir(fnamemodify(l:full_path, ':h'), 'p')\n  call writefile(a:contents, l:full_path)\n  call s:setupproject(printf('%s/src', l:dir), a:path)\n\n  silent exe 'e! ' . a:path\n",
    activeWordIdx: 0,
    activeLineIdx: 0,
    activeTypedWord: "",
    playgroundActive: true
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
      return this.words(this.activeLine)?.length - 1 === this.activeWordIdx;
    }
  },

  methods: {
    words(line) {
      return line && typeof line === "string" ? line.split(" ") : [];
    },

    startTyping() {
      this.playgroundActive = true;
    },

    onKeypress({ event, typedWord }) {
      if (this.isActiveWordLastWordOfActiveLine && event.key !== "Enter") {
        return;
      }

      switch (event.key) {
        case " ":
          event.preventDefault();
          if (typedWord && typedWord.length >= this.activeWord.length) {
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
  },

  mounted() {
    window.addEventListener("click", e => {
      if (document.getElementById("typing-area").contains(e.target)) {
        this.playgroundActive = true;
      } else {
        this.playgroundActive = false;
      }
    });
  }
});
</script>
