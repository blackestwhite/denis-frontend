<template>
  <div class="h-screen flex">
    <!-- <aside class="h-full border-r w-1/4">
      <div class="overflow-y-scroll p-4 min-h-full">
        test
      </div>
    </aside> -->
    <div class="flex flex-col container mx-auto h-full">
      <div class="p-4 border-b">
        <p>
          this is an experimental project built by <a href="https://github.com/blackestwhite" class="text-blue-600">Mahdi Akbari</a>
        </p>
        <p>for remote jobs/projects, contact me, <a href="mailto:pesaregoal@gmail.com" class="text-blue-600">pesaregoal@gmail.com</a></p>
      </div>
      <div class="grow space-y-2 p-4 overflow-y-scroll">
          <div v-for="msg in chat">
              
              <MDC
              :value="msg.content"
              id="mdc"
              :class="{'bg-blue-100':msg.role=='user', 'border': msg.role=='assistant'}"
              class="p-3 rounded border"
              />
              
          </div>
      </div>
      <div class="p-4 flex">
        <input v-model="prompt" type="text" class="border p-4 block w-full border-gray-400 rounded focus:border-black transition-all outline-none" placeholder="enter your prompt here" @keydown="sendViaEnter">
        <button class="p-4 ml-2 border rounded bg-indigo-500 border-indigo-800" @click="sendPrompt">
          send
        </button>
      </div>
    </div>
  </div>
</template>
<script>
export default{
  data() {
    return {
      prompt: '',
      chat: []
    }
  },

  methods: {
    async sendPrompt() {
      if (this.prompt == '') {
        alert('please fill the prompt');
        return;
      }
      this.chat.push({ role: "user", content: this.prompt });
      this.prompt = ''
      const response = await $fetch('/api/v1/gen', {method: 'post', body:{content: this.chat}})
      if (response.ok) {
        this.chat.push(response.result.choices[0].message)
      }
    },

    sendViaEnter(e) {
      if (e.code != "Enter") return
      this.sendPrompt()
    }
  }
}
</script>
<style>
code{
    @apply bg-gray-200 p-1 rounded mx-1
}
pre code {
    @apply block bg-slate-800 text-gray-100 my-2 p-3 rounded
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