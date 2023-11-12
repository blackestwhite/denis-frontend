<template>
    <button
    type="button"
    class="bg-gray-300 flex items-center px-5 py-3 rounded transition-all w-auto"
    @click="handleClick"
    :class="{'cursor-not-allowed': loading, [iclass]: true}"
    :disabled="loading"
    >
        <slot></slot>
        <Transition name="slide" mode="out-in">
            <div v-if="loading" class="flex items-center">
                <span class="ml-2 w-5 h-5 border-2 border-white/50 border-t-black rounded-full spin inline-block duration-75"></span>
            </div>
        </Transition>
    </button>
</template>
  
  <script>
  export default {
    props: ['act', 'iclass'],
  
    data() {
      return {
        loading: false,
      };
    },
  
    methods: {
      async handleClick() {
        this.loading = true
        await this.act()
        this.loading = false
      },
    },
};
</script>

<style>
.spin{
    animation: spin 0.5s linear infinite;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
.slide-enter-active,
.slide-leave-active {
  transition: all 0.15s;
}

.slide-enter, .slide-leave-to {
    transform: translateY(10px);
    opacity: 0;
}
</style>