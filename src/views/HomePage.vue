<script setup>
import { computed } from 'vue'
import AnimeCard from '@/components/AnimeCard.vue'
import { useAnimeRoulette } from '@/composables/useAnimeRoulette'
import WatchList from '@/components/WatchList.vue'

const {
  anime,
  loading,
  error,
  spin,
  cooldownLeft,
  addToWatchlist,
  watchlist,
  isInWatchlist,
  removeFromWatchlist,
} = useAnimeRoulette()

const spinDisabled = computed(() => loading.value || cooldownLeft.value > 0)

const spinLabel = computed(() => {
  if (loading.value) {
    return 'Spinning...'
  }

  if (cooldownLeft.value > 0) {
    return `Cooldown ${cooldownLeft.value}s`
  }

  return 'SPIN 🎰'
})
</script>

<template>
  <main
    class="min-h-screen bg-[radial-gradient(circle_at_10%_20%,#e879f9,transparent_35%),radial-gradient(circle_at_90%_0%,#fb7185,transparent_25%),linear-gradient(160deg,#1e1b4b,#581c87,#9d174d)] px-4 py-8 text-black sm:px-6 lg:px-8"
  >
    <div class="mx-auto max-w-7xl">
      <header class="mb-8">
        <p class="text-xs font-semibold tracking-[0.3em] text-red-800/90 uppercase">Project #4</p>
        <h1 class="mt-2 text-4xl font-black text-black sm:text-5xl">Anime Roulette Machine</h1>
        <p class="mt-2 max-w-3xl text-sm text-black sm:text-base">
          Spin the reel, request a random anime from Jinkan with VueUse useFech, and learnhow REST
          APIs signal rate limiting with HTTP 429.
        </p>
      </header>

      <div class="grid gap-6 lg:grid-cols-[1.2fr_0.8fr]">
        <section class="space-y-5">
          <div
            class="rounded-3xl border border-fuchsia-400/50 bg-fuchsia-950/30 p-5 shadow-[0_0_35px_rgba(232,121,249,0.35)] backdrop-blur-md"
          >
            <div class="flex flex-col gap-3 sm:flex-row sm:items-center sm:justify-between">
              <div>
                <h2 class="text-xl font-bold text-black">Roulette</h2>
                <p class="text-sm text-orange-300">
                  Pull the lever for your next random anime recommendation.
                </p>
              </div>
              <button
                type="button"
                :disabled="spinDisabled"
                class="cursor-pointer rounded-full border border-green-300 bg-pink-500/20 px-6 py-3 text-base font-black tracking-wide text-green-400 hover:bg-cyan-400/30 disabled:cursor-not-allowed disabled:opacity-60"
                @click="spin"
              >
                {{ spinLabel }}
              </button>
            </div>
            <p
              v-if="cooldownLeft > 0"
              class="mt-4 rounded-xl border border-amber-800/50 bg-amber-200/10 px-3 py-2 text-sm font-semibold text-amber-300"
            >
              Rate-limited. Try again in {{ cooldownLeft }}s.
            </p>
          </div>
          <AnimeCard
            :loading="loading"
            :error="error"
            :anime="anime"
            :in-watchlist="Boolean(anime && isInWatchlist(anime.mal_id))"
            @add="addToWatchlist"
          />
        </section>
        <WatchList
          :items="watchlist"
          @remove="removeFromWatchlist"
        />
      </div>
    </div>
  </main>
</template>
