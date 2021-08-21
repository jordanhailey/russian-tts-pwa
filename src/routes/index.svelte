<script>
  import { onMount, tick } from "svelte";

  $: synth = null;
  $: input = "В полной мере совершай свое служение (2 Тим. 4:5)";
  $: voices = [];
  $: pitch = 1;
  $: rate = 1;
  $: selectedVoice = null;
  $: utterance = null;

  $: {
      if (utterance) {
        utterance.voice = voices[selectedVoice];
        utterance.text = input;
        utterance.rate = rate;
        utterance.pitch = pitch;
        utterance.volume = 1;
      }
    }
  
  onMount(()=>{
    initTTS();
  })

  async function initTTS(){
    synth = await window.speechSynthesis;
    tick();  
    // populate voices array
    populateVoiceList();
    if (speechSynthesis.onvoiceschanged !== undefined) {
      speechSynthesis.onvoiceschanged = populateVoiceList;
    }
    tick();  
    utterance = new SpeechSynthesisUtterance(input);
    utterance.onpause = function pausedTTS(e) {
      const char = e.utterance.text.charAt(e.charIndex);
      console.log(`TTS paused at character ${e.charIndex}/${e.utterance.text.length}, which is ${char}.`);
    }
  }

  async function populateVoiceList() {
    // Get voices generated from the window.speechSynthesis Object
    const ttsVoices = await synth.getVoices().filter(voice=>{
      return /ru/i.test(voice.lang) && !/eSpeak/i.test(voice.name)
    });
    voices = ttsVoices;
    if (voices.length === 0) return;
    selectedVoice = 0;
  }

  function playTTS(e){
    console.log({synth,utterance});
    synth.speak(utterance);
  }

    function pauseTTS(e){
      speechSynthesis.pause();
      console.log(speechSynthesis);
      debugger;
  } 

</script>

<h1>Russian Text-To-Speech</h1>
<div class="form-container">
  <h2>Speech synthesiser</h2>
  <p>Enter some text in the input below and press return to hear it. You may change voices using the dropdown menu.</p>

  <form on:submit|preventDefault={playTTS}>
    <textarea bind:value={input}/>
    <div>
      <label for="rate">Rate (higher value = slower speech rate)</label><input type="range" min="0.5" max="2" bind:value={rate} step="0.1" id="rate">
      <div>{rate}</div>
    </div>
    <div>
      <label for="pitch">Pitch</label><input type="range" min="0.1" max="2" bind:value={pitch} step="0.1" id="pitch">
      <div>{pitch}</div>
    </div>
    <select bind:value={selectedVoice}>
      {#each voices as {lang, name, default:dflt},idx}
        <option data-lang={lang} data-name={name} value={idx}>{name} | {lang}{dflt ? " -- DEFAULT" : ""}</option>
      {/each}
    </select>
    <input type="submit" value="play"/>
  </form>
  <button on:click={pauseTTS}>pause</button>
</div>
<!-- 
  woman 1 = rue-local 
  woman 2 = ru-RU-language 
  woman 3 = ruc-local 
  woman 4 = dfc-local 
  woman 5 = dfc-network 
  woman 6 = rue-network
  woman 7 = ruc-network
  man 1 = rud-local 
  man 2 = ruf-local 
  man 3 = ruf-network
  man 4 = rud-network
-->
