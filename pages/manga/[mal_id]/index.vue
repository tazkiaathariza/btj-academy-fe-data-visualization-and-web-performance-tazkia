<template>
  <div>
    <NuxtLink to="/manga" class="text-teal-500 my-4 mb-5">&#x2190; Back</NuxtLink>
    <p class="text-sm font-light my-3">Detail of manga ID : {{ $route.params.mal_id }}</p>
    <div class="max-w-screen-lg mx-auto py-4">
      <div class="grid grid-cols-1 md:grid-cols-12 lg:grid-cols-12 gap-4">
          <!-- Kolom 1 -->
          <div class="col-span-12 md:col-span-3 lg:col-span-3">
            <NuxtImg
              :alt="item?.title || 'Default Alt Text'"
              :src="item?.images?.webp?.image_url || 'default-image-url.jpg'"
              class="border rounded max-h-[300px] w-full"
            />
          </div>
          <!-- Kolom 2 -->
          <div class="col-span-12 md:col-span-9 lg:col-span-9">
            <h2 class="text-2xl font-bold mb-1 text-teal-800">{{ item.title }}</h2>
            <p class="text-md font-normal mb-1">Rating: {{ item.score }}</p>
            <p class="text-md font-normal mb-5">Chapter: {{ item.chapters }}</p>
            <p class="text-md font-normal mb-1">{{ item.synopsis }}</p>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
definePageMeta({ 
  layout: 'home',
});

export default {
  data() {
    return {
      item: {}
    };
  },
  mounted() {
    this.fetchAnimeList();
  },
  methods: {
    async fetchAnimeList() {
      const resp = await fetch(`https://api.jikan.moe/v4/manga/${this.$route.params.mal_id}`);
      const body = await resp.json();
      this.item = body.data;
      console.log(body);
    }
  }
};
</script>