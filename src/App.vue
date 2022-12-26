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
const sliderReturn = ref({ 
  previousIdx: 2, 
  idx: 0, 
  nextIdx: 1, 
  content: "Jan",

  enabled: true,
  readOnly: false,
  filled: false
});

// data -> [ { index, nextIdx, content } ]
const someData = [ 
  { previousIdx: 2, idx: 0, nextIdx: 1, content: "Jan",  enabled: true, readOnly: false, filled: false}, 
  { previousIdx: 0, idx: 1, nextIdx: 2, content: "Fev", enabled: false, readOnly: false, filled: false}, 
  { previousIdx: 1, idx: 2, nextIdx: 0, content: "Mar", enabled: false,  readOnly: false, filled: false} 
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
      <Slider v-model="sliderReturn" :data="someData"/>
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
