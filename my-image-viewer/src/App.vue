<template>
  <div id="app" class="p-6">
    <h1 class="text-2xl mb-4">Media Viewer</h1>

    <input
      v-model="folderName"
      placeholder="Enter folder name (e.g. 55SilverLakeDrive/)"
      class="border p-2 mr-2"
    />
    <button @click="fetchMedia" class="bg-blue-500 text-white p-2">Get Media</button>

    <p v-if="error" class="text-red-500 mt-2">{{ error }}</p>

    <div
      v-if="mediaUrls.length"
      class="mt-6 grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4"
    >
      <div v-for="url in mediaUrls" :key="url">
        <img
          v-if="isImage(url)"
          :src="url"
          alt="Image"
          class="w-full rounded shadow"
        />
        <video
          v-else
          controls
          class="w-full rounded shadow"
        >
          <source :src="url" type="video/mp4" />
          Your browser does not support the video tag.
        </video>
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
    const mediaUrls = ref([]);
    const error = ref('');

    const fetchMedia = async () => {
      if (!folderName.value) {
        error.value = 'Please enter a folder name.';
        return;
      }

      error.value = '';
      mediaUrls.value = [];

      console.log('Fetching media for folder:', folderName.value);

      try {
        const response = await axios.get(
          'https://p2tc3ra7n3.execute-api.us-east-2.amazonaws.com/Prod/image',
          {
            params: {
              folder: folderName.value,
            },
          }
        );

        console.log('Raw response data:', response.data);

        if (response.data.urls && response.data.urls.length > 0) {
          const cleanedUrls = response.data.urls.map((url) =>
            url.split('?')[0]
          );

          console.log('Cleaned URLs:', cleanedUrls);

          mediaUrls.value = cleanedUrls;
        } else {
          error.value = 'No media found.';
          console.log('No URLs returned from API.');
        }
      } catch (err) {
        console.error('Fetch failed:', err);
        error.value = 'Failed to fetch media.';
      }
    };

    const isImage = (url) => {
      const imageExtensions = ['.jpg', '.jpeg', '.png', '.gif', '.bmp', '.webp'];
      const isImg = imageExtensions.some((ext) =>
        url.toLowerCase().endsWith(ext)
      );
      console.log(`Checking if image: ${url} => ${isImg}`);
      return isImg;
    };

    return {
      folderName,
      mediaUrls,
      error,
      fetchMedia,
      isImage,
    };
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
}
</style>
