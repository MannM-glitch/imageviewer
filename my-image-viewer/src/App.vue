<template>
  <div id="app" class="p-6">
    <h1 class="text-2xl mb-4">Image Viewer</h1>

    <input v-model="folderName" placeholder="Enter folder name (e.g. 55SilverLakeDrive/)" class="border p-2 mr-2" />
    <button @click="fetchImages" class="bg-blue-500 text-white p-2">Get Images</button>

    <p v-if="error" class="text-red-500 mt-2">{{ error }}</p>

    <div v-if="imageUrls.length" class="mt-6 grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
      <div v-for="url in imageUrls" :key="url">
        <img :src="url" alt="Image" class="w-full rounded shadow" />
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const folderName = ref('');
    const imageUrls = ref([]);
    const error = ref('');

    const fetchImages = async () => {
      if (!folderName.value) {
        error.value = 'Please enter a folder name.';
        return;
      }

      error.value = '';
      imageUrls.value = [];

      try {
        const response = await axios.get('https://p2tc3ra7n3.execute-api.us-east-2.amazonaws.com/Prod/image', {
          params: {
            folder: folderName.value,
          },
        });

        if (response.data.urls && response.data.urls.length > 1) {
          // Clean URLs by removing query string and exclude the first URL
          imageUrls.value = response.data.urls
            .slice(1) // Skip the first URL
            .map((url) => url.split('?')[0]); // Keep only the base URL
        } else {
          error.value = 'No images found or the first image was excluded.';
        }
      } catch (err) {
        console.error(err);
        error.value = 'Failed to fetch images.';
      }
    };

    return {
      folderName,
      imageUrls,
      error,
      fetchImages,
    };
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
}
</style>
