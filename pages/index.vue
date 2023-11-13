<template>
  <div class="h-screen flex">
    <!-- <aside class="h-full border-r w-1/4">
      <div class="overflow-y-scroll p-4 min-h-full">
        test
      </div>
    </aside> -->
    <div class="flex flex-col container mx-auto h-full">
      <div class="py-4 mb-4 border-b">
        <div class="bg-gray-100 p-4 rounded-2xl">
          <div class="w-14 h-14 rounded-full bg-black/10 flex items-center justify-center">
            <IconCSS name="mdi:info" class="text-2xl"/>
          </div>
          <p class="mt-4">
            Denis is an AI chat bot, powered by OpenAI for programming purposes.
          </p>
          <p class="mt-2">
            built by
            <a href="https://github.com/blackestwhite" target="_blank" class="text-blue-600">
              <IconCSS name="mdi:github" class="mb-1"/>
              Mahdi Akbari
            </a>
          </p>
        </div>
      </div>
      <div class="grow space-y-2 p-4 overflow-y-scroll" id="chat">
          <div v-for="msg in chat">
              
              <MDC
              :value="msg.content"
              id="mdc"
              :class="{'bg-blue-100':msg.role=='user', 'border': msg.role=='assistant'}"
              class="p-3 rounded-xl border"
              />
              
          </div>
      </div>
      <div class="py-4 flex">
        <input v-model="prompt" type="text" class="border p-4 block w-full border-gray-400 rounded-xl focus:border-black transition-all outline-none" placeholder="enter your prompt here" @keydown="sendViaEnter">
        <ibutton id="ibutton" :iclass="`p-4 ml-2 rounded-xl bg-slate-500 shadow-lg shadow-slate-500/50 text-white`" :act="sendPrompt">
          Send
        </ibutton>
      </div>
    </div>
  </div>
</template>
<script>
export default{
  data() {
    return {
      prompt: '',
      chat: [],
      waiting: false,
    }
  },

  methods: {
    async sendPrompt() {
      try {
        if (this.waiting) {
          alert('please wait for previous request to complete')
          return
        }
        if (this.prompt == '') {
          alert('please fill the prompt');
          return;
        }
        this.waiting = true
        this.chat.push({ role: "user", content: this.prompt });
        this.prompt = ''
        const response = await $fetch('/api/v1/gen', {method: 'post', body:{content: this.chat}})
        if (response.ok) {
          this.chat.push(response.result.choices[0].message)
        } else {
          alert("you can send 20 prompt every hour")
        }
      } catch (error) {
        alert("a problem occured, please try again later")
      } finally {
        this.waiting = false
      }
    },

    sendViaEnter(e) {
      if (e.code != "Enter") return
      document.getElementById('ibutton').click()
    }
  }
}
</script>
<style>
::-webkit-scrollbar {
  width: 8px;
  border-radius: 8px;
}

/* Track */
::-webkit-scrollbar-track {
  background: #f1f1f1;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: #888;
  border-radius: 4px;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #555;

}
code {
  direction: ltr;
  @apply bg-gray-200 p-1 rounded mx-1
}
pre code {
  direction: ltr;
  @apply block bg-slate-800 text-gray-100 my-2 p-3 rounded overflow-x-scroll
}
ul {
  list-style: disc;
  @apply px-4
}
ol {
  list-style: decimal;
  @apply px-4
}
</style>