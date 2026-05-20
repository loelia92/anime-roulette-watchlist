<script setup>
import { computed, ref, watch } from 'vue'

const props = defineProps({
  anime: {
    type: Object,
    default: null,
  },
  loading: {
    type: Boolean,
    default: false,
  },
  error: {
    type: String,
    default: '',
  },
  inWatchlist: {
    type: Boolean,
    default: false,
  },
})

const emit = defineEmits(['add'])

const synopsisExpanded = ref(false)

const animeImage = computed(() => {
  return (
    props.anime?.images?.jpg?.large_image_url ||
    props.anime?.images?.jpg?.image_url ||
    props.anime?.images?.webp?.large_image_url ||
    props.anime?.images?.webp?.image_url ||
    ''
  )
})

const synopsis = computed(() => {
  return props.anime?.synopsis || 'No synopsis available yet.'
})

const needsTruncation = computed(() => synopsis.value.length > 240)

const visibleSynopsis = computed(() => {
  if (synopsisExpanded.value || !needsTruncation.value) {
    return synopsis.value
  }

  return `${synopsis.value.slice(0, 240)}...`
})

watch(
  () => props.anime?.mal_id,
  () => {
    synopsisExpanded.value = false
  },
)
</script>

<template>
  <section
    class="rounded-3xl border border-fuchsia-400/50 bg-fuchsia-950/30 p-5 shadow-[0_0_35px_rgba(232,121,249,0.35)] backdrop-blur-md"
  >
    <div
      v-if="props.loading"
      class="space-y-4"
    >
      <div class="flex items-center gap-3 text-green-300">
        <div
          class="h-5 w-5 animate-spin rounded-full border-2 border-green-400 border-t-transparent"
        ></div>
        <p class="font-semibold tracking-wide">Shuffling anime reels...</p>
      </div>

      <div class="grid grid-cols-3 gap-2">
        <div class="h-3 animate-pulse rounded bg-slate-700"></div>
        <div class="h-3 animate-pulse rounded bg-slate-700"></div>
        <div class="h-3 animate-pulse rounded bg-slate-700"></div>
      </div>
      <div class="h-72 animate-pulse rounded-2xl bg-slate-800"></div>
    </div>

    <div
      v-else-if="error"
      class="rounded-2xl border border-red-300/50 bg-red-500/10 p-4 text-red-100"
    >
      <h3 class="text-lg font-semibold">Spin failed</h3>
      <p class="mt-2 text-sm text-orange-300/90">{{ error }}</p>
    </div>

    <div
      v-else-if="anime"
      class="space-y-4"
    >
      <div class="bg-clear-500/20 overflow-hidden rounded-2xl border border-green-300/80">
        <img
          v-if="animeImage"
          :src="animeImage"
          :alt="anime.title"
          class="h-80 w-full bg-pink-900/50 object-contain"
          loading="lazy"
        />
        <div
          v-else
          class="flex h-80 items-center justify-center bg-pink-800 text-orange-300"
        >
          No poster available
        </div>
      </div>

      <div>
        <h2 class="text-2xl font-black text-black">{{ anime.title }}</h2>
        <p class="mt-1 text-sm text-orange-300">
          Score: <span class="font-semibold text-green-300">{{ anime.score ?? 'N/A' }}</span> ·
          Episodes:
          <span class="font-semibold text-cyan-300">{{ anime.episodes ?? 'Unknown' }}</span> ·
          Rating: <span class="font-semibold text-pink-300">{{ anime.rating || 'Unknown' }}</span>
        </p>
      </div>

      <p class="text-sm leading-relaxed text-orange-300">
        {{ visibleSynopsis }}
        <button
          v-if="needsTruncation"
          type="button"
          class="ml-2 text-green-300 underline-offset-4 hover:underline"
          @click="synopsisExpanded = !synopsisExpanded"
        >
          {{ synopsisExpanded ? 'Show less' : 'Read more' }}
        </button>
      </p>

      <div class="flex flex-wrap gap-3">
        <button
          type="button"
          class="rounded-full border border-green-300/60 bg-pink-500/20 px-4 py-2 text-sm font-semibold text-black transition hover:bg-green-400 disabled:cursor-not-allowed disabled:opacity-50"
          :disabled="inWatchlist"
          @click="emit('add', anime)"
        >
          {{ inWatchlist ? 'In Watchlist' : 'Add to Watchlist' }}
        </button>

        <a
          :href="anime.url"
          target="_blank"
          rel="noopener noreferrer"
          class="hover:border-/40 rounded-full border border-green-500 px-4 py-2 text-sm font-semibold text-black transition"
        >
          Open on MAL
        </a>
      </div>
    </div>

    <div
      v-else
      class="rounded-2xl border border-green-400/60 bg-pink-500/20 p-6 text-center text-orange-300"
    >
      Pull the lever to request your first random anime.
    </div>
  </section>
</template>
