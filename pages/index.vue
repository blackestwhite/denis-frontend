<template>
  <div class="h-screen flex">
    <!-- <aside class="h-full border-r w-1/4">
      <div class="overflow-y-scroll p-4 min-h-full">
        test
      </div>
    </aside> -->
    <div class="flex flex-col container mx-auto h-full">
      <div class="p-4 mb-4 border-b md:px-0">
        <div class="bg-gray-100 p-4 rounded-2xl">
          <div class="w-14 h-14 rounded-full bg-black/10 flex items-center justify-center">
            <IconCSS name="mdi:info" class="text-2xl"/>
          </div>
          <p class="mt-4 text-sm">
            Denis is an AI chat bot, powered by OpenAI for programming purposes.
          </p>
          <p class="mt-2 text-sm">
            built by
            <a href="https://github.com/blackestwhite" target="_blank" class="text-blue-600">
              <IconCSS name="mdi:github" class="mb-1"/>
              Mahdi Akbari
            </a>
            , For remote projects/jobs or reserving ads on this website, contact me
          </p>
        </div>
      </div>
      <div class="grow space-y-2 p-4 overflow-y-scroll" id="chat">
          <div v-for="msg in chat">
              
            <MDC
            :value="msg.content"
            :class="{'bg-blue-100':msg.role=='user', 'border': msg.role=='assistant'}"
            class="mdc p-3 rounded-xl border"
            />
              
          </div>
          <MDC
            v-if="completion!=''"
            :value="completion"
            id="mdc"
            class="mdc p-3 rounded-xl border"
          />
      </div>
      <div class="p-4 flex md:px-0">
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
      completion: '',
    }
  },

  methods: {
    async sendPrompt() {
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
        const eventSource = new EventSource(`/api/v1/get/${response.result.id}`)
        eventSource.onmessage = (event) => {
          // Handle incoming events and update your UI
          console.log(event.data);
          var obj = {}
          if (event.data == "[DONE]") {
            this.chat.push({
              role: 'assistant',
              content: this.completion
            })
            this.completion = ''
            this.waiting = false
            return
          }
          obj = JSON.parse(event.data)
          
          if (obj.choices[0].finish_reason == "stop") return
          let content = obj.choices[0].delta.content
          if (content != "" || content != undefined) {
            this.completion += content
          }
        };
      } else {
        alert("you can send 20 prompt every hour")
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