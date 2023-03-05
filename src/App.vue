<template>
  <Header @setCurrentComponent="setCurrentComponent" :currentComponent="currentComponent"/>
  <component :is="currentComponent"></component>
</template>

<script>
import Header from "@/components/Header";
import Calculator from "@/components/Calculator";
import Converter from "@/components/Converter";
import {computed} from "vue";

export default {
  name: 'App',
  components: {
    Header,
    Calculator,
    Converter,
  },
  data() {
    return {
      currentComponent: 'Calculator',
      currentTheme: 'light',
      themeClasses: {
        main: true,
        dark: false,
      },
      body: document.body,
    }
  },
  provide() {
    return {
      theme: computed(() => this.currentTheme),
      setTheme: this.changeTheme
    }
  },
  watch: {
    currentTheme() {
      this.body.classList.toggle('dark')
    }
  },
  created() {
    this.body.classList.add('main')
  },
  methods: {
    setCurrentComponent(name) {
      this.currentComponent = name;
    },

    changeTheme(nameTheme) {
      this.currentTheme = nameTheme;
    }
  }
}
</script>

<style>
  .main {
    background: #f5f5f5;
    transition-duration: 1s;
  }

  .dark {
    background: #283637;
  }

  .light {
    background: #f5f5f5!important;
    transition-duration: 1s;
  }

  .light::-webkit-scrollbar {
    border: 1px solid black;
  }

  body {
    margin: 0;
    padding: 0;
  }
</style>
