<script setup>
defineProps({
  items: {
    type: Array,
    default: () => [],
  },
})

const emit = defineEmits(['remove'])
</script>

<template>
  <aside
    class="rounded-3xl border border-fuchsia-400/50 bg-fuchsia-950/30 p-5 shadow-[0_0_35px_rgba(232,121,249,0.35)] backdrop-blur-md"
  >
    <div class="mb-4 flex items-center justify-between">
      <h2 class="text-xl font-bold text-black">Watchlist</h2>
      <span class="rounded-full bg-pink-700 px-3 py-1 text-xs font-semibold text-orange-300">{{
        items.length
      }}</span>
    </div>

    <p
      v-if="!items.length"
      class="rounded-2xl border border-green-400/70 bg-pink-500/20 p-4 text-sm text-orange-300"
    >
      No picks saved yet. Spin and add an anime to build your watchlist.
    </p>

    <ul
      v-else
      class="space-y-3"
    >
      <li
        v-for="item in items"
        :key="item.mal_id"
        class="rounded-2xl border border-green-400/70 bg-pink-500/20 p-3"
      >
        <div class="flex gap-3">
          <img
            v-if="item.image"
            :src="item.image"
            :alt="item.title"
            class="h-20 w-14 rounded-md object-cover"
            loading="lazy"
          />
          <div
            v-else
            class="flex h-20 w-14 items-center justify-center rounded-md bg-blue-700 text-xs text-pink-300"
          >
            N/A
          </div>

          <div class="min-w-0 flex-1">
            <h3 class="truncate text-sm font-semibold text-pink-300">{{ item.title }}</h3>
            <p class="mt-1 text-xs text-blue-400">
              ⭐ {{ item.score ?? 'N/A' }} · Ep {{ item.episodes ?? '?' }} ·
              {{ item.rating || 'Unrated' }}
            </p>
            <div class="mt-3 flex flex-wrap gap-2">
              <a
                :href="item.url"
                target="_blank"
                rel="noopener noreferrer"
                class="text-xs font-semibold text-green-300 hover:underline"
              >
                MAL Link
              </a>
              <button
                type="button"
                class="text-xs font-semibold text-rose-600 hover:underline"
                @click="emit('remove', item.mal_id)"
              >
                Remove
              </button>
            </div>
          </div>
        </div>
      </li>
    </ul>
  </aside>
</template>
