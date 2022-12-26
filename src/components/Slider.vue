<script setup lang="ts">
import { computed } from "vue";
/** Props:
 * dados - meses
 * dado atual - mês atual
 * dados preenchidos - meses preenchidos
 * dados indisponíveis - meses indisponíveis
 */

interface DataObj {
  previousIdx: number,
  idx: number,
  nextIdx: number,
  content: string,

  enabled: boolean,
  readOnly: boolean,
  filled: boolean
}

const props = defineProps<{
  data: Array<DataObj>,
  modelValue: DataObj,

  
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
<button @click="handleReturnClick">Voltar</button>

<ul v-for="key of data">
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