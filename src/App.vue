<script setup>
import { ref, computed } from "vue";
import { useClipboard } from "@vueuse/core";

const text = ref("");

const convertSinglePrepositionForCopy = (value) => {
  return value.replace(
    /(\s|\&nbsp;)([aiueoksvzAIUEOKSVZ]|[0-9]\.?)\s/g,
    "$1$2&nbsp;"
  );
};

const convertSinglePreposition = (value) => {
  return value.replace(
    /(\s|\&nbsp;)([aiueoksvzAIUEOKSVZ]|[0-9]\.?)\s/g,
    "$1$2<span>&amp;nbsp;</span>"
  );
};

const convertToNonBreakingHyphensInPredefinedWords = (value) => {
  return value.replace(/-/g, "<span>&amp;#8209;</span>");
};

const convertToNonBreakingHyphensInPredefinedWordsForCopy = (value) => {
  return value.replace(/-/g, "&#8209;");
};

const convertedText = computed(() => {
  let returnVal = convertSinglePreposition(text.value);
  returnVal = convertToNonBreakingHyphensInPredefinedWords(returnVal);

  return returnVal;
});

const convertedTextForCopy = computed(() => {
  let returnVal = convertSinglePrepositionForCopy(text.value);
  returnVal = convertToNonBreakingHyphensInPredefinedWordsForCopy(returnVal);

  return returnVal;
});

const { copy, copied, isSupported } = useClipboard({
  source: convertedTextForCopy,
});
</script>

<template>
  <main class="px-12 bg-gray-800 h-screen w-screen">
    <section class="max-w-xl pt-64 mx-auto">
      <label for="comment" class="block mb-1 text-sm font-medium text-gray-100"
        >Text to be converted</label
      >

      <div class="mt-1">
        <textarea
          placeholder="Insert text to be converted..."
          v-model="text"
          rows="10"
          name="comment"
          id="comment"
          class="shadow-sm text-lg focus:ring-indigo-500 focus:border-indigo-500 p-2 block w-full bg-gray-200 sm:text-sm border-gray-300 rounded-md"
        ></textarea>
      </div>

      <div v-if="convertedText">
        <label
          for="comment"
          class="block mt-5 mb-1 text-sm font-medium text-gray-100"
          >Converted text</label
        >
        <div class="bg-gray-200 p-3 rounded-md">
          <pre
            class="break-words"
          > <code class="bg-gray-200" v-html="convertedText"></code></pre>
        </div>
      </div>

      <div v-if="isSupported && convertedText" class="flex justify-end">
        <button
          @click="copy()"
          type="button"
          class="inline-flex mt-4 items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
        >
          {{ copied ? "Copied ????" : "Copy" }}
        </button>
      </div>
    </section>
  </main>
</template>

<style>
pre {
  font-size: 15px;
  white-space: pre-wrap;
  white-space: -moz-pre-wrap;
  white-space: -pre-wrap;
  white-space: -o-pre-wrap;
  word-wrap: break-word;
}

code > span {
  background: yellow;
}
</style>
