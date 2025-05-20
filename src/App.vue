<template>
  <div :class="['app', darkMode ? 'dark' : '']">
    <h1><i class="fas fa-language"></i> Language Translator</h1>

    <div class="selectors">
      <label>From:</label>
      <select v-model="sourceLang">
        <option v-for="(name, code) in languages" :value="code" :key="code">
          {{ name }}
        </option>
      </select>

      <label>To:</label>
      <select v-model="targetLang">
        <option v-for="(name, code) in languages" :value="code" :key="code">
          {{ name }}
        </option>
      </select>
    </div>

    <input v-model="inputText" placeholder="Enter text" />
    <div class="buttons">
      <button @click="translateText"><i class="fas fa-language"></i> Translate</button>
      <button v-if="translatedText" @click="speak(translatedText)">
        <i class="fas fa-volume-up"></i> Speak
      </button>
      <button @click="clearHistory"><i class="fas fa-trash-alt"></i> Clear History</button>
      <button @click="toggleDarkMode"><i class="fas fa-moon"></i> Dark Mode</button>
    </div>

    <p v-if="translatedText" class="translation">
      Translation: <strong>{{ translatedText }}</strong>
    </p>

    <h2>History (Last 10)</h2>
    <ul class="history">
      <li v-for="(item, index) in history" :key="index">
        {{ item.source }} ({{ item.sourceLang }}) → {{ item.result }} ({{ item.targetLang }})
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputText: '',
      translatedText: '',
      sourceLang: 'en',
      targetLang: 'hi',
      history: [],
      darkMode: false,
      languages: {
        en: 'English',
        hi: 'Hindi',
        es: 'Spanish',
        fr: 'French',
        de: 'German',
        ru: 'Russian',
        zh: 'Chinese',
        ja: 'Japanese',
        ar: 'Arabic',
        bn: 'Bengali',
        te: 'Telugu',
        ta: 'Tamil',
        ur: 'Urdu',
        mr: 'Marathi',
        gu: 'Gujarati',
        kn: 'Kannada'
      }
    };
  },
  methods: {
    async translateText() {
      if (!this.inputText.trim()) return;

      try {
        const res = await fetch(
          `https://api.mymemory.translated.net/get?q=${encodeURIComponent(this.inputText)}&langpair=${this.sourceLang}|${this.targetLang}`
        );
        const data = await res.json();
        this.translatedText = data.responseData.translatedText;

        this.history.unshift({
          source: this.inputText,
          result: this.translatedText,
          sourceLang: this.languages[this.sourceLang],
          targetLang: this.languages[this.targetLang]
        });

        if (this.history.length > 10) this.history.pop();

        this.inputText = '';
      } catch (error) {
        alert('Translation failed. API unavailable.');
        console.error(error);
      }
    },
    clearHistory() {
      this.history = [];
    },
    speak(text) {
      const speech = new SpeechSynthesisUtterance(text);
      speech.lang = this.targetLang;
      window.speechSynthesis.speak(speech);
    },
    toggleDarkMode() {
      this.darkMode = !this.darkMode;
    }
  }
};
</script>
<style>
.app {
  max-width: 750px;
  margin: 30px auto;
  padding: 30px;
  font-family: 'Segoe UI', sans-serif;
  border-radius: 20px;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
  background: linear-gradient(to right, #fdfbfb, #ebedee);
  transition: background-color 0.3s, color 0.3s;
}

.dark {
  background: linear-gradient(to right, #232526, #414345);
  color: #f1f1f1;
}

h1 {
  text-align: center;
  font-size: 2em;
  margin-bottom: 30px;
  color: #2c3e50;
}

.dark h1 {
  color: #f9ca24;
}

.selectors, .buttons {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 15px;
  margin-bottom: 20px;
}

select, input {
  padding: 12px;
  border-radius: 10px;
  border: 2px solid #3498db;
  font-size: 1em;
  transition: border 0.3s;
  min-width: 160px;
}

input:focus, select:focus {
  border-color: #9b59b6;
  outline: none;
}

button {
  padding: 12px 20px;
  font-size: 1em;
  background: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%);
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  transition: transform 0.2s ease, background 0.3s;
}

button:hover {
  transform: scale(1.05);
  background: linear-gradient(135deg, #ff512f 0%, #dd2476 100%);
}

.translation {
  margin-top: 20px;
  font-size: 1.3em;
  text-align: center;
  color: #16a085;
}

.dark .translation {
  color: #f6e58d;
}

h2 {
  margin-top: 30px;
  text-align: center;
  color: #8e44ad;
}

.dark h2 {
  color: #f0932b;
}

.history {
  list-style: none;
  padding: 0;
  margin-top: 10px;
  background: #f0f0f0;
  border-radius: 12px;
  padding: 15px;
}

.dark .history {
  background: #333;
}

.history li {
  padding: 8px 12px;
  border-bottom: 1px solid #ccc;
}

.history li:last-child {
  border-bottom: none;
}

.dark .history li {
  border-color: #555;
}
</style>

