<script setup lang="ts">
import { computed, ref, watch } from "vue";
/** Props:
 * dados - meses
 * dado atual - mês atual
 * dados preenchidos - meses preenchidos
 * dados indisponíveis - meses indisponíveis
 */

interface DataObj {
  content: string,

  enabled: boolean,
  readOnly: boolean,
  filled: boolean
}

const props = defineProps<{
  data: Array<DataObj>,
  modelValue: string | DataObj,
  carouselLength: number

  
  // filled?: Array<{}>,
  // enable?: Array<{}>,
  // readonly: Array<{}>
}>();

/** Events:
 * dado selecionado - mês selecionado
 */
const emit = defineEmits<{
  (event: "update:modelValue", value: object): void;
}>();

// const startPointer = ref(props.modelValue.idx);
// const endPointer = ref();

// remove index, informar o tamanho do carousel
const dataLen = props.data.length;
const lastIndex = dataLen - 1;
const data = computed(() => {
  props.data.map((element, index, originalData) => {
    let previousIdx = index - 1 < 0 ? lastIndex : index - 1; 
    let nextIdx = index + 1 > lastIndex ? 0 : index + 1;

    return {
      ...element,
      previousIdx,
      idx: index,
      nextIdx
    }
  });
})

console.log("AQUI", tryOut)

const dataArray = computed<DataObj[] | void>(() => {
  // if (props.modelValue.idx <= 3) {
  //   return props.data.slice(0,4);
  // } else if (props.modelValue.idx <= 7) {
  //   return props.data.slice(4,8);
  // } else {
  //   return props.data.slice(8,12)
  // }

  // let startPointer = props.modelValue.idx;
  // let endPointer = startPointer + props.carouselLength;

  let carousel = props.carouselLength
  const loopTimes = props.data.length / carousel;

  for (let index = 0; index < loopTimes; index++) {
    // 4 <= 4, 8 <= 8, 12 <= 12
    if(props.modelValue.idx < carousel * (index + 1)) {
      return props.data.slice(carousel * index, carousel * (index + 1))
    }      
  }
  // if (startPointer % props.carouselLength === 0 || startPointer % 0 === 0) {
  //   // 0, 1, 2
  //   for (let index = 0; index < loopTimes; index++) {
  //     // 4 <= 4, 8 <= 8, 12 <= 12
  //     if(props.modelValue.idx < carousel * (index + 1)) {
  //       return props.data.slice(carousel * index, carousel * (index + 1))
  //     }      
  //   } 
    
  // }


  // for (let index = 0; index < loopTimes; index++) {
  //   if(index == carousel*index -1) {
  //     return props.data.slice(carousel*(index))
  //   }    
  // }

  /**
   * index (começa em zero) <= carouselLength -1
   * index (começa em zero) <= 2 * carouselLength -1
   * 
   * quantas vezes o length é divisível? 3x - len 12, carouselLen 4.
   * const times = len / 4 = 3
   * 
   * teremos 3 condicionais:
   *  - carouselLen x 1 -1
   *  - carouselLen x 2 -1
   *  - carouselLen x 3 -1
   * 
   * e dentro de cada teremos o slice 
   *  - carouselLen *0, carouselLen *1
   *  - carouselLen *1, carouselLen *2
   *  - carouselLen *3, carouselLen *4
   * 
   */

  // let startPointer = props.modelValue.idx;
  // let endPointer = startPointer + props.carouselLength;

  // if (startPointer % props.carouselLength === 0 || startPointer % 0 === 0) {
  //   console.log("troca quadrante")
    
  // }


  // if ( props.data.length - props.modelValue.idx > props.carouselLength) {
  //   return props.data.slice(props.modelValue.idx, props.modelValue.idx + props.carouselLength) //mes 1 até 4?
  // }
  // console.log(4%4)
  // console.log(props.modelValue.idx + 1, props.carouselLength, (props.modelValue.idx + 1) % props.carouselLength)
  // if((props.modelValue.idx + 1) % props.carouselLength === 0){
  //   return props.data.slice(props.modelValue.idx, props.modelValue.idx + props.carouselLength)
  // }
  // return props.data.slice(0,4)

});

watch(dataArray, () => {
  console.log("nova renderização")
})

// preciso retornar isso.
// data -> [ { index: x, mês: x, selecionado: false, disponível: true, preenchido: false, proximoIndex: x} ]
// function handleClick() {
//   console.log("clicked")
//   emit("update:modelValue", { a: !(props.modelValue?.a) });
// }

function handleClick(key: DataObj) {
  if(key.idx === props.modelValue.idx) return;
  //console.log("Funcionou")
  emit("update:modelValue", { ...props.data[key.idx] })
}

function getPreviousIndex(previousIdx: number): number  {
  let data = props.data;

  // console.log("previousIdx", previousIdx, "data previousIdx", data[previousIdx])

  // idx = 1
  if(data[previousIdx].enabled == false) {
    previousIdx = getPreviousIndex(data[previousIdx].previousIdx);
  }

  return previousIdx;
}

function getNextIndex(nextIdx: number): number  {
  let data = props.data;

  // console.log("previousIdx", previousIdx, "data previousIdx", data[previousIdx])

  // idx = 1
  if(data[nextIdx].enabled == false) {
    nextIdx = getNextIndex(data[nextIdx].nextIdx);
  }

  return nextIdx;
}


function handleReturnClick() {
  // mar - prev = 1, idx = 2, nxt = 0
  let previousIndex = getPreviousIndex(props.modelValue.previousIdx);

  console.log(previousIndex)
  if(previousIndex === props.modelValue.idx) return;

  emit("update:modelValue", { ...props.data[previousIndex] });
}

function handleNextClick() {
  let nextIndex = getNextIndex(props.modelValue.nextIdx);

  if(nextIndex === props.modelValue.idx) return;

  emit("update:modelValue", { ...props.data[nextIndex] });
}

/**
 * data -> [ { index: x, mês: x, selecionado: false, disponível: true, preenchido: false, proximoIndex: x} ]
 * 
 * data -> [ { index, nextIdx, content } ]
 * selected -> number (current index)
 * unavailable -> [indexes]
 * filled -> [indexes]
 */

const style = computed(() => {

});

</script>
<template>
<ul v-for="key of dataArray">
  <li>{{ key }}</li>
</ul>

<button @click="handleReturnClick">Voltar</button>

<ul v-for="key of dataArray">
  <li 
    :class="[
      (props.modelValue.idx === key.idx ? `border` : ``),
      (key.enabled === false ? `disabled` : ``),
      (key.filled === true ? `filled` : ``),
      (key.readOnly === true ? `readOnly` : ``)
    ]" 
    @click="handleClick(key)"
  >
    {{ key.content }}
  </li>
</ul>

<button id="next" @click="handleNextClick">Próximo</button>

<!-- <button @click="handleClick">Botão</button> -->
</template>
<style>
button#next {
  margin-left: 15px;
}

.filled {
  background-color: orange;
}

.border {
  border: solid white 1px;
}
</style>