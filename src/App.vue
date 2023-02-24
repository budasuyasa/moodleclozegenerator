<script setup>
import { ref, watch } from 'vue'
import useClipboard from 'vue-clipboard3'
import IconClipboard from './components/icons/IconClipboard.vue'

const { toClipboard } = useClipboard()
const question = ref('')
const items = ref([null, null, null, null, null])
const reason = ref('')
const finalResult = ref('')
const rawquestion = ref('')


watch(rawquestion, (val) => {
  let q = val.split('\n')
  q.forEach((item, index) => {
    items.value[index] = item.slice(3)
  })
})

watch(reason, (val) => {
  finalResult.value = question.value + '{1:MULTICHOICE_VS:' + items.value.join('~') + '} Alasan: {0:SHORTANSWER:~%100%' + reason.value + '}'
})

watch(question, (val) => {
  finalResult.value = question.value + '{1:MULTICHOICE_VS:' + items.value.join('~') + '} Alasan: {0:SHORTANSWER:~%100%' + reason.value + '}'
})

watch(items, (val) => {
  finalResult.value = question.value + '{1:MULTICHOICE_VS:' + items.value.join('~') + '} Alasan: {0:SHORTANSWER:~%100%' + reason.value + '}'
}, { deep: true })



function addItems() {
  items.value.push('')
}
function toggleCorrect(index) {
  let pos = items.value[index].indexOf('%100%')
  if (pos > -1) {
    items.value[index] = items.value[index].replace('%100%', '')
    return
  }
  items.value[index] = '%100%' + items.value[index]
}

const copyToClipboard = async () => {
  try {
    await toClipboard(finalResult.value)
  } catch (e) {
    console.log(e)
  }
}


</script>
<template>
  <div class="max-w-7xl mx-auto p-5 border rounded shadow-xl my-12">
    <h1 class="text-3xl font-bold text-center my-4">Cloze Question Generator</h1>
    <div class="sm:grid sm:grid-cols-2 sm:gap-8 flex flex-col">
      <form action="#" class="flex flex-col">
        <label class="text-gray-400">Pertanyaan</label>
        <input v-model="question" class="p-4 border border-gray-500 rounded shadow w-full" type="text" name="question"
          id="question" placeholder="Pertanyaan" />

        <label class="text-gray-400">Opsi Mentah</label>
        <textarea v-model="rawquestion" class="w-full border border-gray-500 p-4 rounded h-48"></textarea>

        <div v-for="(opt, index) in items" :key="index" class="my-1">
          <label class="text-gray-400">Opsi {{ index + 1 }}</label>
          <div class="flex flex-row">
            <input v-model="items[index]" class="p-4 w-7/12 border border-gray-500 rounded shadow" type="text"
              name="option[]" id="option" />
            <input @change="toggleCorrect(index)" type="checkbox" name="items[]" id="correct" class="mt-4 ml-2" />
          </div>
        </div>
        <label class="text-gray-400">Jawaban Alasan</label>
        <input placeholder="Jawaban Alasan" type="text" v-model="reason"
          class="p-4 border border-gray-500 rounded shadow w-full" />
        <button type="button" @click="addItems"
          class="mt-2 bg-green-400 rounded p-3 border-green-50 shadow text-white active:bg-green-600 hover:bg-green-500 ">Add
          Option</button>
      </form>
      <div id="result" class="w-full mt-2 border-gray-500 flex flex-col">
        <textarea v-on:focus="$event.target.select()" ref="clone" readonly
          class="w-full rounded font-mono border border-gray-200 p-4 h-80">{{ finalResult }}</textarea>
        <button @click="copyToClipboard"
          class="hover:bg-gray-50 active:bg-gray-100 w-fit rounded-sm mb-2 p-2 ml-auto hover:shadow-md active:shadow-sm">
          <IconClipboard />
        </button>
      </div>
    </div>
  </div>
</template>
