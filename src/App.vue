<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'
import confetti from 'canvas-confetti'

// --- Configuration ---
const partnerName = "Batsy"
const valentineDate = new Date('2026-02-14T00:00:00') // Set to next Valentine's Year

// --- State ---
const timeLeft = ref({ days: 0, hours: 0, minutes: 0, seconds: 0 })
const isValentine = ref(false)
const currentQuote = ref("")

// --- Data: Love Quotes ---
const quotes = [
  "In all the world, there is no heart for me like yours.",
  "Every time I see you, I fall in love all over again.",
  "You are my sun, my moon, and all my stars.",
  "I love you more than words can wield the matter, dearer than eye-sight, space, and liberty.",
  "To the world you may be one person, but to one person you are the world.",
  "My heart is and always will be yours.",
  "You make my heart smile.",
  "Life is better with you by my side."
]

// --- Logic: Countdown ---
const updateTimer = () => {
  const now = new Date()
  const diff = valentineDate - now

  if (diff <= 0) {
    isValentine.value = true
    timeLeft.value = { days: 0, hours: 0, minutes: 0, seconds: 0 }
    return
  }

  timeLeft.value = {
    days: Math.floor(diff / (1000 * 60 * 60 * 24)),
    hours: Math.floor((diff / (1000 * 60 * 60)) % 24),
    minutes: Math.floor((diff / 1000 / 60) % 60),
    seconds: Math.floor((diff / 1000) % 60)
  }
}

// --- Logic: Random Quote ---
const generateQuote = () => {
  const randomIndex = Math.floor(Math.random() * quotes.length)
  currentQuote.value = quotes[randomIndex]
}

// --- Logic: Animation ---
const launchConfetti = () => {
  const duration = 15 * 1000;
  const animationEnd = Date.now() + duration;
  const defaults = { startVelocity: 30, spread: 360, ticks: 60, zIndex: 0 };

  const randomInRange = (min, max) => Math.random() * (max - min) + min;

  const interval = setInterval(function () {
    const timeLeft = animationEnd - Date.now();

    if (timeLeft <= 0) {
      return clearInterval(interval);
    }

    const particleCount = 50 * (timeLeft / duration);
    confetti({ ...defaults, particleCount, origin: { x: randomInRange(0.1, 0.3), y: Math.random() - 0.2 } });
    confetti({ ...defaults, particleCount, origin: { x: randomInRange(0.7, 0.9), y: Math.random() - 0.2 } });
  }, 250);
}

// --- Lifecycle ---
let timerInterval
onMounted(() => {
  updateTimer()
  generateQuote()
  timerInterval = setInterval(updateTimer, 1000)

  // Auto-trigger confetti if it's already the big day
  if (isValentine.value) launchConfetti()
})

onUnmounted(() => {
  clearInterval(timerInterval)
})
</script>

<template>
  <div
    class="min-h-screen flex flex-col items-center justify-center bg-gradient-to-br from-pink-100 via-rose-100 to-pink-200 text-rose-800 p-4 overflow-hidden relative">

    <div class="absolute top-10 left-10 text-pink-300 opacity-50 animate-bounce text-4xl">♥</div>
    <div class="absolute bottom-20 right-10 text-rose-300 opacity-50 animate-pulse text-6xl">♥</div>
    <div class="absolute top-1/2 left-5 text-pink-200 opacity-30 animate-ping text-2xl">♥</div>

    <div
      class="relative z-10 w-full max-w-md bg-white/60 backdrop-blur-md shadow-xl rounded-3xl p-8 border border-white/50 text-center transition-all duration-500 hover:shadow-2xl hover:scale-[1.02]">

      <h1 class="text-4xl md:text-5xl font-bold mb-2 text-rose-600 drop-shadow-sm font-serif">
        {{ isValentine ? "Happy Valentine's!" : "Hey " + partnerName + "!" }}
      </h1>
      <p class="text-rose-400 font-medium mb-8 uppercase tracking-widest text-xs">
        {{ isValentine ? "I love you so much" : "Countdown to our day" }}
      </p>

      <div v-if="!isValentine" class="grid grid-cols-4 gap-2 mb-10">
        <div class="flex flex-col items-center p-2 bg-white/50 rounded-xl shadow-sm">
          <span class="text-2xl md:text-3xl font-bold text-rose-500">{{ timeLeft.days }}</span>
          <span class="text-[10px] uppercase text-rose-400">Days</span>
        </div>
        <div class="flex flex-col items-center p-2 bg-white/50 rounded-xl shadow-sm">
          <span class="text-2xl md:text-3xl font-bold text-rose-500">{{ timeLeft.hours }}</span>
          <span class="text-[10px] uppercase text-rose-400">Hrs</span>
        </div>
        <div class="flex flex-col items-center p-2 bg-white/50 rounded-xl shadow-sm">
          <span class="text-2xl md:text-3xl font-bold text-rose-500">{{ timeLeft.minutes }}</span>
          <span class="text-[10px] uppercase text-rose-400">Mins</span>
        </div>
        <div class="flex flex-col items-center p-2 bg-white/50 rounded-xl shadow-sm">
          <span class="text-2xl md:text-3xl font-bold text-rose-500">{{ timeLeft.seconds }}</span>
          <span class="text-[10px] uppercase text-rose-400">Secs</span>
        </div>
      </div>

      <div v-else class="mb-10 animate-pulse-slow">
        <p class="text-lg text-rose-700 italic">"Today is all about you and me."</p>
        <button @click="launchConfetti"
          class="mt-4 px-6 py-2 bg-rose-500 text-white rounded-full shadow-lg hover:bg-rose-600 transition">
          Celebrate Again 🎉
        </button>
      </div>

      <div class="bg-pink-50/80 rounded-2xl p-6 relative">
        <span class="absolute top-2 left-4 text-4xl text-rose-200 font-serif">“</span>
        <p class="text-rose-800 font-medium italic relative z-10 px-2">
          {{ currentQuote }}
        </p>
        <span class="absolute bottom-[-10px] right-4 text-4xl text-rose-200 font-serif">”</span>
      </div>

      <button @click="generateQuote"
        class="mt-6 text-sm text-rose-400 hover:text-rose-600 underline decoration-rose-300 underline-offset-4 transition-colors">
        Give me another reason I love you
      </button>

    </div>

    <footer class="absolute bottom-4 text-xs text-rose-300 opacity-80">
      Made with ❤️ for Batsy
    </footer>

  </div>
</template>

<style scoped>
/* Optional: Add a custom font like 'Playfair Display' or 'Dancing Script' in your index.html for extra flair */
</style>