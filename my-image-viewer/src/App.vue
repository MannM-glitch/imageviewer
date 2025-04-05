<template>
  <div id="app" class="p-6">
    <h1 class="text-2xl mb-4">Image Viewer</h1>
    <input v-model="imageName" placeholder="Enter image name" class="border p-2 mr-2" />
    <button @click="fetchImage" class="bg-blue-500 text-white p-2">Get Image</button>
    <div v-if="imageUrl" class="mt-4">
      <h2>Image Preview:</h2>
      <img :src="imageUrl" alt="Fetched Image" class="mt-2 max-w-lg" />
    </div>
    <p v-if="error" class="text-red-500">{{ error }}</p>
  </div>
</template>

<script>
import { ref } from 'vue';
import axios from 'axios';

export default {
  setup() {
    // Reactive state
    const imageName = ref('');
    const imageUrl = ref('');
    const error = ref('');

    // Fetch image method
    const fetchImage = async () => {
      if (!imageName.value) {
        error.value = 'Please enter an image name.';
        return;
      }
      error.value = '';
      imageUrl.value = '';
  
      
      try {
        const response = await axios.get(`https://p2tc3ra7n3.execute-api.us-east-2.amazonaws.com/Prod/test?name=${encodeURIComponent(imageName.value)}`);
        if(response.data.url) {
          imageUrl.value = response.data.url;
          console.log(imageUrl.value);
        } else {
          error.value = 'Image not found or no URL returned.';
        }
      } catch (err) {
        error.value = 'Failed to fetch image.';
      }
    };

    // Return state and methods to template
    return {
      imageName,
      imageUrl,
      error,
      fetchImage,
    };
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
}
</style>