<template>
  <div>
    <h2>ByteAcads OCR part</h2>

    <p>Select a file to edit</p>
    <input
      type="file"
      name="img"
      id="img"
      accept="image/png, image/jpeg"
      @change="uploadImg"
    />
    <button @click="runOcr" v-show="previewImage">run OCR</button>
    <div id="result">
      <div>
        <h2>Uploaded image</h2>
        <img id="img-display" :src="previewImage" />
      </div>
      <div>
        <h2>Recognized text</h2>
        <p>{{ ocrText }}</p>
      </div>
    </div>
  </div>
</template>
<script setup lang="ts">
import { ref } from "vue";
import { createWorker } from "tesseract.js";

let previewImage: any = ref(null);
let ocrText = ref("");

const uploadImg = (e) => {
  const image = e.target.files[0];
  const reader = new FileReader();
  reader.readAsDataURL(image);
  reader.onload = (e) => {
    previewImage.value = e.target.result;
  };
};

const worker = createWorker({
  logger: (m) => console.log(m),
});

const runOcr = async () => {
  const img = document.getElementById("img");
  await worker.load();
  await worker.loadLanguage("eng");
  await worker.initialize("eng");
  const {
    data: { text },
  } = await worker.recognize(img);
  ocrText.value = text;
  console.log(text);
};
</script>

<style scoped>
#img {
  padding: 10px;
}
#result {
  display: flex;
  justify-content: space-around;
}
</style>
