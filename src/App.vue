<script setup lang="ts">
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import Slider from './components/Slider.vue'
import { ref, reactive } from 'vue';

const sliderSelectedData = ref();

function getSliderSelect(e: any) {
  console.log("AQUI", e)
  sliderSelectedData.value = e
}

// const value = ref({ a: true })
const sliderReturn = ref({ content: "Jan"});

// data -> [ { index, nextIdx, content } ]
const someData = [ 
  { content: "Jan",  enabled: true, readOnly: false, filled: false}, 
  { content: "Fev", enabled: true, readOnly: false, filled: false}, 
  { content: "Mar", enabled: true,  readOnly: false, filled: false}, 
  { content: "Abr", enabled: true,  readOnly: false, filled: false}, 
  { content: "Mai", enabled: true,  readOnly: false, filled: false}, 
  { content: "Jun", enabled: true,  readOnly: false, filled: false}, 
  { content: "Jul", enabled: true,  readOnly: false, filled: false}, 
  { content: "Ago", enabled: true,  readOnly: false, filled: false}, 
  { content: "Set", enabled: true,  readOnly: false, filled: false}, 
  { content: "Out", enabled: true,  readOnly: false, filled: false}, 
  { content: "Nov", enabled: true,  readOnly: false, filled: false}, 
  { content: "Dez", enabled: true,  readOnly: false, filled: false}, 
];

</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />
    </div>

  </header>
  
  <main>
    <TheWelcome />
    <div class="slider">
      <Slider v-model="sliderReturn" :data="someData" :carouselLength="4"/>
    </div>
    {{ sliderReturn }}
  </main>

</template>

<style scoped>
.slider {
  display: flex;
}

header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
