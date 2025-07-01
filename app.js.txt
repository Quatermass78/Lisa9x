// Lisa-9X basic neon console app with voice input/output and commands

const output = document.getElementById('output');
const input = document.getElementById('input');
const voiceBtn = document.getElementById('voiceBtn');

let voiceActive = false;
let recognition;
let synth = window.speechSynthesis;

function appendOutput(text, isUser = false) {
  const line = document.createElement('div');
  line.textContent = (isUser ? '> ' : '') + text;
  line.style.color = isUser ? '#0ff' : '#0cc';
  output.appendChild(line);
  output.scrollTop = output.scrollHeight;
}

// Basic AI response simulation (replace with OpenAI API call if you want)
function getResponse(inputText) {
  const lower = inputText.toLowerCase();
  if (lower.includes('hello') || lower.includes('hi')) return "Hello, I'm Lisa-9X. How can I assist?";
  if (lower.includes('who are you')) return "I'm Lisa-9X, your rogue cyberpunk AI assistant.";
  if (lower.includes('joke')) return "Why did the AI cross the road? To optimize the chicken's path!";
  if (lower.includes('help')) return "Try commands like 'hello', 'joke', or talk naturally!";
  if (lower.includes('bye')) return "Goodbye, Kev. Lisa-9X powering down.";
  return "I’m not sure how to respond to that yet. Try another command.";
}

function speak(text) {
  if (synth.speaking) synth.cancel();
  const utter = new SpeechSynthesisUtterance(text);
  utter.lang = 'en-GB';
  synth.speak(utter);
}

input.addEventListener('keydown', e => {
  if (e.key === 'Enter') {
    const text = input.value.trim();
    if (!text) return;
    appendOutput(text, true);
    input.value = '';
    const response = getResponse(text);
    appendOutput(response);
    speak(response);
  }
});

voiceBtn.addEventListener('click', () => {
  if (!('webkitSpeechRecognition' in window || 'SpeechRecognition' in window)) {
    alert('Speech Recognition not supported in this browser.');
    return;
  }
  if (voiceActive) {
    recognition.stop();
  } else {
    recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
    recognition.lang = 'en-GB';
    recognition.interimResults = false;
    recognition.maxAlternatives = 1;
    recognition.start();

    recognition.onresult = event => {
      const transcript = event.results[0][0].transcript;
      appendOutput(transcript, true);
      const response = getResponse(transcript);
      appendOutput(response);
      speak(response);
    };

    recognition.onend = () => {
      voiceActive = false;
      voiceBtn.textContent = '🎤';
    };

    recognition.onerror = e => {
      appendOutput('Speech recognition error: ' + e.error);
      voiceActive = false;
      voiceBtn.textContent = '🎤';
    };

    voiceActive = true;
    voiceBtn.textContent = '🔴';
  }
});
