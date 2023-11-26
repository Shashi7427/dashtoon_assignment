<template>
  <q-page class="q-mt-lg">
    <div class="flex flex-center q-mb-lg">
      <q-card class="my-card">
        <q-card-section>
          <div class="text-h6">Welcome to AI Image Generator!</div>
          <div class="text-subtitle2">
            Create stunning images with the power of AI.
          </div>
        </q-card-section>

        <q-card-section>
          <q-input
            rounded
            outlined
            v-model="imageDescription"
            label="Describe your image"
          />
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="Generate" color="primary" @click="generateImage" />
        </q-card-actions>
      </q-card>
    </div>
    <div v-if="imageUrl">
      <img :src="imageUrl" alt="Generated Image" class="generated-image" />
      <q-btn label="Download Image" @click="downloadImage" />
    </div>
    <div class="justify-center q-pa-lg" v-if="showPanel">
      <div class="q-col-gutter-sm row items-start">
        <div
          class="col-4"
          v-for="(url, index) in imageUrls.slice(4, 7)"
          :key="index"
        >
          <q-img :src="url || 'https://cdn.quasar.dev/img/parallax2.jpg'">
            <div class="absolute-bottom-right text-subtitle2">{{ caption }}</div>
          </q-img>
        </div>
        <div
          class="col-3"
          v-for="(url, index) in imageUrls.slice(0, 4)"
          :key="index"
        >
          <q-img :src="url || 'https://cdn.quasar.dev/img/parallax2.jpg'">
            <div class="absolute-bottom-right text-subtitle2" v-if="!url">{{ caption }}</div>
          </q-img>
        </div>
        <div
          class="col-4"
          v-for="(url, index) in imageUrls.slice(7, 11)"
          :key="index"
        >
          <q-img :src="url || 'https://cdn.quasar.dev/img/parallax2.jpg'">
            <div class="absolute-bottom-right text-subtitle2">{{ caption }}</div>
          </q-img>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import { ref } from "vue";
export default defineComponent({
  name: "IndexPage",
  data() {
    return {
      imageDescription: "",
      imageUrl: null,
      imageUrls: ref(Array(10).fill(0)),
      caption :ref("Error fetching the image"),
      showPanel : ref(false)
    };
  },
  methods: {
    // function for fetching the images;
    async query(data) {
      console.log("requesting");
      console.log(process.env.TOKEN);
      const response = await fetch(
        "https://xdwvg9no7pefghrn.us-east-1.aws.endpoints.huggingface.cloud",
        {
          headers: {
            Accept: "image/png",
            Authorization:
              "Bearer "+process.env.TOKEN,
            "Content-Type": "application/json",
          },
          method: "POST",
          body: JSON.stringify(data),
        }
      );
      console.log("got the result");
      const result = await response.blob();
      return result;
    },
    generateImage() {
      // we request 10 images
      // as these are async operations as and when we get the response we update our imageUrls array
      // this will eventually dispaly the images;
      for (let i = 0; i < 10; i++) {
        this.query({ inputs: this.imageDescription })
          .then((response) => {
            // Process the successful response
            this.imageUrls[i] = URL.createObjectURL(response);
            this.showPanel = true;
          })
          .catch((error) => {
            // Handle errors, including 502 errors
            console.error("Error fetching image:", error);
            this.imageUrls[i] = null; // Example error handling
          });
      }
    },
  },
});
</script>

<style lang="sass" scoped>
.my-card
  width: 100%
  max-width: 350px
.generated-image
  width: 100%
  max-width: 350px
</style>
