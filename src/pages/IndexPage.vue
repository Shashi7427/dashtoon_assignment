<template>
  <q-page class="q-mt-lg">
    <!-- main UI : showing an input box along with the application description -->
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
          <q-btn flat :label=buttonLabel color="primary" @click="generateImage" :disable=disableButton />
        </q-card-actions>
      </q-card>
    </div>

    <!-- showing images only if we get at least one response -->
    <div class="justify-center q-pa-lg" v-if="showPanel">
      <!-- we show tree panels of size 3,4,3 showing in total 10 images -->
      <div class="q-col-gutter-sm row items-start">

        <div
          class="col-md-4 col-12"
          v-for="(url, index) in imageUrls.slice(4, 7)"
          :key="index"
        >
          <q-img :src="url || 'https://cdn.quasar.dev/img/parallax2.jpg'">
            <!-- we are showing the download option if the image is fetched otherwire an error message is shown -->
            <div class="absolute-bottom-right text-subtitle2" :class="url ? 'cursor-pointer' : ''" @click="() => downloadImage(url)">{{ url ? "download" : caption }}</div>
          </q-img>
        </div>

        <div
          class="col-md-3 col-12"
          v-for="(url, index) in imageUrls.slice(0, 4)"
          :key="index"
        >
          <q-img :src="url || 'https://cdn.quasar.dev/img/parallax2.jpg'">
            <div class="absolute-bottom-right text-subtitle2" :class="url ? 'cursor-pointer' : ''" @click="() => downloadImage(url)">{{ url ? "download" : caption }}</div>
          </q-img>
        </div>

        <div
          class="col-md-4 col-12"
          v-for="(url, index) in imageUrls.slice(7, 11)"
          :key="index"
        >
          <q-img :src="url || 'https://cdn.quasar.dev/img/parallax2.jpg'">
            <div class="absolute-bottom-right text-subtitle2" :class="url ? 'cursor-pointer' : ''" @click="() => downloadImage(url)">{{ url ? "download" : caption }}</div>
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
      imageDescription: "", // variable to store the use query
      imageUrls: ref(Array(10).fill(null)), // an array to 10 image urls which are initialized to null
      caption :ref("Error fetching the image"), // error message shown if there is error or longer response time
      showPanel : ref(false), // variable to show image panel if atleast one request is fetched
      disableButton : ref(false), // to disable the button if the request is made
      buttonLabel : ref("GENERATE") // button label if the to fetch the data;
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
        this.buttonLabel = "Loading";
        this.disableButton = true;
        this.query({ inputs: this.imageDescription })
          .then((response) => {
            // Process the successful response
            this.imageUrls[i] = URL.createObjectURL(response);
            this.showPanel = true;
            this.buttonLabel = "GENERATE";
            this.disable = false;
          })
          .catch((error) => {
            // Handle errors, including 502 errors
            console.error("Error fetching image:", error);
            this.imageUrls[i] = null; // Example error handling
          });
      }
    },
    downloadImage :(url) => {
      // if url is not fetched then return
      if (!url) {
        return;
      }
      const link = document.createElement('a');
      link.href = url;
      link.download = 'generated-image.png';
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }
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
