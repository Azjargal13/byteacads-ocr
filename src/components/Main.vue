<template>
  <div>
    <h2>ByteAcads OCR part</h2>

    <p>Select a file to edit</p>
    <input
      type="file"
      name="img"
      id="img"
      accept="image/png, image/jpeg"
      @change="uploadImg($event)"
    />
    <img id="img-display" :src="previewImage" />
  </div>
</template>
<script setup lang="ts">
let previewImage = null;
import { createWorker } from "tesseract.js";
const uploadImg = (e) => {
  const image = e.target.files[0];
  const reader = new FileReader();
  reader.readAsDataURL(image);
  reader.onload = (e) => {
    previewImage = e.target.result;
  };
};
const worker = createWorker({
  logger: (m) => console.log(m),
});

(async () => {
  await worker.load();
  await worker.loadLanguage("eng");
  await worker.initialize("eng");
  const {
    data: { text },
  } = await worker.recognize(
    "https://tesseract.projectnaptha.com/img/eng_bw.png"
  );
  console.log(text);
  await worker.terminate();
})();
</script>

<style scoped></style>
