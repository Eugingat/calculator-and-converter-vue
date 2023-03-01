<template>
  <header :class="{light: isDark}">
    <div>
      <button :class="{ ...btnClasses, btnDark: isDark }" v-for="name of listComponent" :key="name" @click="chooseComponent(name)">
        {{name}}
      </button>
    </div>
    <div :class=" {
        iconsLight: true,
        iconsDark: isDark
      }">
      <SvgIcon v-if="isLightTheme" type="mdi" :path="pathSunIcon" @click="setTheme('dark')"></SvgIcon>
      <SvgIcon v-else type="mdi" :path="pathMoonIcon" @click="setTheme('light')"></SvgIcon>
    </div>
  </header>
</template>

<script>
import SvgIcon from '@jamescoyle/vue-icon';
import { mdiWeatherSunny, mdiWeatherNight   } from '@mdi/js';

export default {
  components: {
    SvgIcon
  },
  // eslint-disable-next-line vue/multi-word-component-names
  name: "Header",
  data() {
    return {
      listComponent: ['Calculator', 'Converter'],
      btnClasses: {
        btn: true,
        isActive: false,
      },
      pathSunIcon: mdiWeatherSunny,
      pathMoonIcon: mdiWeatherNight,
    }
  },
  inject: ['theme', 'setTheme'],
  props: ['currentComponent'],
  emits: ['setCurrentComponent'],
  computed: {
    isLightTheme() {
      return this.theme.value === 'light';
    },
    isDark() {
      return this.theme.value === 'dark'
    }
  },

  methods: {
    chooseComponent(name) {
      this.$emit('setCurrentComponent', name);
    }
  }
}
</script>

<style scoped>
  header {
    max-width: 600px;
    margin: 0 auto;
    box-shadow: 2px 2px 2px #283637;
    background: #283637;
    display: flex;
    justify-content: space-between;
    padding: 5px 10px ;
    margin-top: 40px;
    transition-duration: 1s;
  }

  .iconsLight {
    align-self: center;
    color: #f5f5f5;
  }

  .iconsLight svg {
    width: 31px;
    height: 31px;
  }

  .iconsDark {
    color: #009788;
  }

  .btnDark {
    background: #009788;
    color: #f5f5f5!important;
  }

  .btn {
    padding: 10px 15px;
    color: #009788;
    border: none;
    font-size: 15px;
    font-weight: 600;
    transition-duration: 1s;
  }

  .btn ~ button {
    margin-left: 15px;
  }
</style>