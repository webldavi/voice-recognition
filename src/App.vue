<template>
  <div class="w-full min-h-screen bg-gray-900 flex items-center justify-center font-mono text-white p-2">
    <div class="w-full max-w-xl flex flex-col gap-2 h-max border border-gray-400 rounded-lg p-2">
      <div class="bg-gray-800 p-2 text-center break-words rounded-lg w-full h-max" id="result">
      </div>
      <button id="startButton" class="w-full p-2 h-max font-bold text-xl bg-green-500/50 text-green-300 rounded-lg">Iniciar reconhecimento</button>
      <button @click="copyText" class="w-full p-2 h-max font-bold text-xl bg-gray-600 text-gray-400 rounded-lg">Copiar Texto</button>
    </div>
  </div>
</template>
<script setup>
import {onMounted, ref} from 'vue'

const result = ref("Seu texto")

function copyText(){
  navigator.clipboard.writeText(result.value);
}

onMounted(()=>{
  if("webkitSpeechRecognition" in window){
    let recognition = new webkitSpeechRecognition();
    recognition.cotinuous = true;
    recognition.interimResults = true;
    recognition.lang = "pt-BR"
    const startButton = document.getElementById('startButton')
    startButton.addEventListener('click', ()=>{
      recognition.start();
      result.value = ''
    })

    recognition.onresult = (event)=>{
      for(var i = event.resultIndex; i < event.results.length; i++){
        result.value = event.results[i][0].transcript
      }
      document.getElementById('result').innerHTML = result.value
    }
    recognition.onerror = (event)=>{
      console.log('Error: ', event.error)
    }
    console.log(recognition)
  }
})
</script>