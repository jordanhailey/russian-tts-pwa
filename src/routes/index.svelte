<script>
import { onMount, tick } from "svelte";

let input=""

let synth,
  form,
  voiceSelect,
  pitch,
  pitchValue,
  rate,
  rateValue,
  voices = []

onMount(()=>{
  synth = window.speechSynthesis;
  form = document.querySelector('form');
  voiceSelect = document.querySelector('select');
  pitch = document.querySelector('#pitch');
  pitchValue = document.querySelector('.pitch-value');
  rate = document.querySelector('#rate');
  rateValue = document.querySelector('.rate-value');

  tick();
  // populate voices array
  populateVoiceList();
  if (speechSynthesis.onvoiceschanged !== undefined) {
    speechSynthesis.onvoiceschanged = populateVoiceList;
  }
})

function populateVoiceList() {
  voices = synth.getVoices(); // Generated from the window.speechSynthesis Object
  voices.map((opt)=>{
    const {lang, name, default:dflt} = opt;
    if (/ru/i.test(lang) == false ) return;
    // if (/eSpeak/i.test(name)) return // I don't like the eSpeak voice option
    const option = document.createElement('option');
    option.textContent = `${name} | ${lang}${dflt ? " -- DEFAULT" : ""}`;
    option.setAttribute('data-lang', lang);
    option.setAttribute('data-name', name);
    voiceSelect.appendChild(option);
    console.log({option,opt})
  })
}


</script>

<h1>Russian Text-To-Speech</h1>
<div class="form-container">
<h2>Speech synthesiser</h2>
<p>Enter some text in the input below and press return to hear it. You may change voices using the dropdown menu.</p>

<form>
  <input type="text" bind:value={input}>
  <div>
    <label for="rate">Rate</label><input type="range" min="0.5" max="2" value="1" step="0.1" id="rate">
    <div class="rate-value">1</div>
    <div class="clearfix"></div>
  </div>
  <div>
    <label for="pitch">Pitch</label><input type="range" min="0" max="2" value="1" step="0.1" id="pitch">
    <div class="pitch-value">1</div>
    <div class="clearfix"></div>
  </div>
  <select>

  </select>
</form>
</div>
