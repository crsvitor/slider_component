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

interface DataObj2 extends DataObj {
  previousIdx: number
  idx: number
  nextIdx: number
}

const props = defineProps<{
  data: Array<DataObj>,
  modelValue: {
    content: string
  } | DataObj,
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
const propsData = computed<DataObj2[]>(() => {
  return props.data.map((element, index) => {
    let previousIdx = index - 1 < 0 ? lastIndex : index - 1; 
    let nextIdx = index + 1 > lastIndex ? 0 : index + 1;

    return {
      ...element,
      previousIdx,
      idx: index,
      nextIdx
    }
  });
});

console.log("AQUI", propsData.value)

const propsModel = computed<DataObj2>(() => {
  let data = propsData.value.find((element) => { return element.content === props.modelValue.content })
  emit("update:modelValue", { ...data })
  return data as DataObj2;
});

console.log(propsModel.value)

const dataArray = computed<DataObj2[] | void>(() => {
  // if (props.modelValue.idx <= 3) {
  //   return props.data.slice(0,4);
  // } else if (props.modelValue.idx <= 7) {
  //   return props.data.slice(4,8);
  // } else {
  //   return props.data.slice(8,12)
  // }

  let carousel = props.carouselLength
  const loopTimes = propsData.value.length / carousel;

  for (let index = 0; index < loopTimes; index++) {
    // 4 <= 4, 8 <= 8, 12 <= 12
    if(propsModel.value.idx < carousel * (index + 1)) {
      return propsData.value.slice(carousel * index, carousel * (index + 1))
    }      
  }
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

function handleClick(key: DataObj2) {
  if(key.idx === propsModel.value.idx) return;
  //console.log("Funcionou")
  emit("update:modelValue", { ...propsData.value[key.idx] })
}

function getPreviousIndex(previousIdx: number): number  {
  let data = propsData.value;

  // console.log("previousIdx", previousIdx, "data previousIdx", data[previousIdx])

  // idx = 1
  if(data[previousIdx].enabled == false) {
    previousIdx = getPreviousIndex(data[previousIdx].previousIdx);
  }

  return previousIdx;
}

function getNextIndex(nextIdx: number): number  {
  let data = propsData.value;

  // console.log("previousIdx", previousIdx, "data previousIdx", data[previousIdx])

  // idx = 1
  if(data[nextIdx].enabled == false) {
    nextIdx = getNextIndex(data[nextIdx].nextIdx);
  }

  return nextIdx;
}


function handleReturnClick() {
  // mar - prev = 1, idx = 2, nxt = 0
  let previousIndex = getPreviousIndex(propsModel.value.previousIdx);

  console.log(previousIndex)
  if(previousIndex === propsModel.value.idx) return;

  emit("update:modelValue", { ...propsData.value[previousIndex] });
}

function handleNextClick() {
  let nextIndex = getNextIndex(propsModel.value.nextIdx);

  if(nextIndex === propsModel.value.idx) return;

  emit("update:modelValue", { ...propsData.value[nextIndex] });
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
      (propsModel.idx === key.idx ? `border` : ``),
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