<template>
  <div :class="{ conver: true, light: isDark }">
    <h1> 2222222 </h1>
  </div>
</template>

<script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: "Converter",
  data() {
    return {

    }
  },
  inject: ['theme'],
  computed: {
    isDark() {
      return this.theme.value === 'dark'
    }
  },
}
</script>

<style scoped>
  .conver {
    max-width: 600px;
    margin: 0 auto;
    background: #283637;
    box-shadow: 2px 2px 2px #283637;
    padding: 5px 10px;
    transition-duration: 1s;
  }
</style>