<script setup lang="ts">
import type { formType } from "@/formtype";
import { reactive, ref } from "vue";
import axios from "axios";

const form = reactive<formType>({
  body: "",
  social: "",
  loading: false,
  char: 120,
});

let text = ref("");
let responseCard = ref<HTMLDivElement>();

async function submit() {
  form.loading = true;
  text.value = "";

  responseCard.value?.scrollIntoView({ behavior: "smooth" });

  try {
    const response = await axios.post(
      "https://api.openai.com/v1/completions",
      {
        model: "text-davinci-003",
        prompt: `Generate a ${form.social} bios with this keywords ${form.body} and it should not be more than ${form.char} characters`,
        temperature: 0.7,
        top_p: 1,
        frequency_penalty: 0,
        presence_penalty: 0,
        max_tokens: Number(form.char),
      },
      {
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer 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`,
        },
      }
    );

    text.value = response.data.choices[0].text;
    form.loading = false;
  } catch (error) {
    form.loading = false;
    console.log(error);
  }
}

// Copy text to clipboard on click
async function copyText(text: string) {
  try {
    await navigator.clipboard.writeText(text);
    alert("Text copied successfully");
  } catch (error) {
    console.log(error);
  }
}
</script>

<template>
  <header>
    <nav
      class="flex justify-between items-center bg-white px-5 py-4 md:px-10 shadow-md md:shadow-sm"
    >
      <h1 class="text-2xl md:text-4xl font-bold text-slate-800">
        <span class="text-primary">&lt;</span>BioGenerator<span
          class="text-primary"
          >/&gt;</span
        >
      </h1>
      <a
        href=""
        class="flex items-center px-2 py-2 md:px-5 gap-2 rounded-xl bg-primary/10"
      >
        <img
          class="w-6 h-6"
          src="src/assets/img/github-icon.svg"
          alt="Github logo"
        />
        <span class="text-primary max-sm:hidden">Star on Github </span>
      </a>
    </nav>
  </header>
  <main
    class="container mx-auto md:mt-10 h-screen"
    :class="{ 'mb-8': form.loading }"
  >
    <div class="md:shadow-md px-5 md:w-[70%] mx-auto bg-slate-50 py-8">
      <h2 class="text-3xl md:text-4xl font-bold text-primary text-center">
        Generate Social Media Bio with AI
      </h2>
      <form class="my-8" @submit.prevent="submit">
        <label for="body" class="block my-2 text-gray-600"
          >Supply keywords of what you do</label
        >
        <textarea
          v-model="form.body"
          class="form-control focus:invalid:border-2 focus:invalid:border-red-600"
          name=""
          id="body"
          cols="30"
          rows="2"
          minlength="8"
          placeholder="e.g Software, developer, frontend"
          required
        ></textarea>

        <label class="block my-2 text-gray-600" for="char"
          >Maximum Characters</label
        >
        <input
          type="number"
          name="char"
          id="char"
          class="form-control mb-4"
          value="120"
        />

        <p class="my-2 text-gray-600">Choose the Social Media Platform</p>
        <div class="flex flex-wrap gap-2 group-radio">
          <label for="facebook" class="cursor-pointer">
            <input
              v-model="form.social"
              type="radio"
              class="hidden peer invalid:border-2 invalid:border-red-600"
              id="facebook"
              name="social"
              value="facebook"
              required
            />
            <div
              class="bg-primary hover:scale-105 transition-all duration-200 peer-checked:bg-black px-4 py-2 shadow-sm rounded-md"
            >
              <span class="text-white">Facebook</span>
            </div>
          </label>
          <label for="twitter" class="cursor-pointer">
            <input
              v-model="form.social"
              type="radio"
              class="hidden peer"
              id="twitter"
              name="social"
              value="twitter"
              required
            />
            <div
              class="bg-primary hover:scale-105 transition-all duration-200 peer-checked:bg-black px-4 py-2 shadow-sm rounded-md"
            >
              <span class="text-white">Twitter</span>
            </div>
          </label>

          <label for="linkedin" class="cursor-pointer">
            <input
              v-model="form.social"
              type="radio"
              class="hidden peer"
              id="linkedin"
              name="social"
              value="linkedin"
              required
            />
            <div
              class="bg-primary hover:scale-105 transition-all duration-200 peer-checked:bg-black px-4 py-2 shadow-sm rounded-md"
            >
              <span class="text-white">Linkedin</span>
            </div>
          </label>
          <label for="tiktok" class="cursor-pointer">
            <input
              v-model="form.social"
              type="radio"
              class="hidden peer"
              id="tiktok"
              name="social"
              value="tiktok"
              required
            />
            <div
              class="bg-primary hover:scale-105 transition-all duration-200 peer-checked:bg-black px-4 py-2 shadow-sm rounded-md"
            >
              <span class="text-white">Tiktok</span>
            </div>
          </label>
        </div>
        <button
          type="submit"
          :class="{ 'bg-primary/50 cursor-wait': form.loading }"
          :disabled="form.loading"
          class="w-full bg-primary px-4 py-3 transition-all duration-200 rounded-md text-white my-6"
        >
          <span v-show="!form.loading">Generate bio</span>

          <div
            class="flex gap-1 justify-center"
            v-show="form.loading"
            role="status"
          >
            <svg
              aria-hidden="true"
              class="w-6 h-6 text-gray-200 animate-spin fill-blue-600"
              viewBox="0 0 100 101"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z"
                fill="currentColor"
              />
              <path
                d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z"
                fill="currentFill"
              />
            </svg>
            <span class="">Processing...</span>
          </div>
        </button>
      </form>

      <div ref="responseCard">
        <div
          v-if="form.loading"
          class="border border-slate-300 shadow rounded-md p-4 w-full mx-auto my-6"
        >
          <div class="animate-pulse flex space-x-4">
            <div class="flex-1 space-y-6 py-1">
              <div class="h-2 bg-slate-500 rounded"></div>
              <div class="space-y-3">
                <div class="grid grid-cols-3 gap-4">
                  <div class="h-2 bg-slate-500 rounded col-span-2"></div>
                  <div class="h-2 bg-slate-500 rounded col-span-1"></div>
                </div>
                <div class="h-2 bg-slate-700 rounded"></div>
              </div>
            </div>
          </div>
        </div>

        <div v-if="text" class="cards my-6">
          <h4 class="text-2xl my-4 text-primary font-bold text-center">
            Generated Bio
          </h4>
          <div
            class="drop-shadow-md bg-slate-100 text-md p-5 rounded-lg cursor-copy"
            @click.prevent="copyText(text)"
          >
            {{ text }}

            <div class="text-xs text-primary mt-2 text-right">
              Click or tap to copy
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
  <footer>
    <div
      class="px-5 py-4 md:px-10 bg-gray-100 md:flex md:items-center md:justify-between"
    >
      <span class="text-sm text-black sm:text-center"
        >Developed 🛠 by
        <strong
          ><a href="https://twitter.com/TijaniEneye"
            >BrainyDeveloper ❤️
          </a></strong
        >
        and Powered by
        <strong>OpenAI</strong>
      </span>
      <div class="flex mt-4 space-x-3 sm:justify-center md:mt-0">
        <a href="#" class="text-black hover:text-gray-900">
          <svg
            class="w-5 h-5"
            fill="currentColor"
            viewBox="0 0 24 24"
            aria-hidden="true"
          >
            <path
              d="M8.29 20.251c7.547 0 11.675-6.253 11.675-11.675 0-.178 0-.355-.012-.53A8.348 8.348 0 0022 5.92a8.19 8.19 0 01-2.357.646 4.118 4.118 0 001.804-2.27 8.224 8.224 0 01-2.605.996 4.107 4.107 0 00-6.993 3.743 11.65 11.65 0 01-8.457-4.287 4.106 4.106 0 001.27 5.477A4.072 4.072 0 012.8 9.713v.052a4.105 4.105 0 003.292 4.022 4.095 4.095 0 01-1.853.07 4.108 4.108 0 003.834 2.85A8.233 8.233 0 012 18.407a11.616 11.616 0 006.29 1.84"
            />
          </svg>
          <span class="sr-only">Twitter page</span>
        </a>
        <a
          href="https://twitter.com/TijaniEneye"
          class="text-black hover:text-gray-900"
        >
          <svg
            class="w-5 h-5"
            fill="currentColor"
            viewBox="0 0 24 24"
            aria-hidden="true"
          >
            <path
              fill-rule="evenodd"
              d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.531 1.032 1.531 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z"
              clip-rule="evenodd"
            />
          </svg>
          <span class="sr-only">GitHub account</span>
        </a>
      </div>
    </div>
  </footer>
</template>

<style scoped></style>
