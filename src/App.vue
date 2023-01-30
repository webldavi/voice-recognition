<template>
  <div class="w-full min-h-screen bg-gray-900 flex items-center justify-center font-mono text-white p-2">
    <div class="w-full max-w-xl flex flex-col gap-2 h-max border border-gray-400 rounded-lg p-2">
      <textarea class="bg-gray-800 h-max overflow-hidden p-2 text-center break-words rounded-lg w-full h-max" id="result" v-model="result">
      </textarea>
      <div class="w-full h-max p-1 flex items-center justify-end">
        <span class="text-lg text-green-500 font-bold">{{ status }}</span>
      </div>
      <div class="w-full flex flex-row flex-wrap justify-between gap-y-4">
        <button id="startButton" class="button p-2 h-auto font-bold text-xl bg-green-500/50 text-green-300 rounded-lg">Iniciar
          reconhecimento</button>
        <button id="stopButton" class="button p-2 h-auto font-bold text-xl bg-red-500/50 text-red-300 rounded-lg">Parar
          Reconhecimento</button>
        <button @click="copyText" class="button p-2 h-auto font-bold text-xl bg-gray-600 text-gray-400 rounded-lg">Copiar
          Texto</button>
        <button @click="result = ''"
          class="button p-2 h-auto font-bold text-xl bg-yellow-500/50 text-yellow-500 rounded-lg">Limpar Texto</button>
      </div>
    </div>
  </div>
</template>
<script setup>
import {onMounted, ref, watch} from 'vue'

const result = ref("")
const status = ref("")
const cacheResult = ref("")
function copyText(){
  navigator.clipboard.writeText(result.value);
}

watch(result, ()=>{
  let textarea = document.getElementById('result')
  textarea.style.height = textarea.scrollHeight + "px";
})

onMounted(async()=>{
  if("webkitSpeechRecognition" in window){
    let recognition = new webkitSpeechRecognition();
    recognition.cotinuous = true;
    recognition.interimResults = true;
    recognition.lang = "pt-BR"
    const startButton = document.getElementById('startButton')
    const stopButton = document.getElementById('stopButton')


    startButton.addEventListener('click', ()=>{
      cacheResult.value = result.value
      recognition.start();
      status.value = 'Reconhecendo...'
    })

    stopButton.addEventListener('click', ()=>{
      recognition.stop()
      status.value = ''
    })

    recognition.onresult = (event)=>{
      let newResult = ''
      for(var i = event.resultIndex; i < event.results.length; i++){
        newResult = event.results[i][0].transcript
      }
      result.value = cacheResult.value + " " + newResult
      
    }
    recognition.onspeechend = () => {
      recognition.stop();
      status.value = ''
    }
    recognition.onerror = (event)=>{
      console.log('Error: ', event.error)
    }
    
  }
})
</script>

<style>
.button{
  width: calc(50% - 0.5rem);
}
</style>