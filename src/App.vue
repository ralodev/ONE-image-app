<script setup>
  import template from '@/assets/template.jpg';
  import { ref } from 'vue';

  const uploadedImage = ref(null);

  function downloadImage() {
    // Create a canvas to draw the template and uploaded image
    const canvas = document.createElement('canvas');
    canvas.width = 750;
    canvas.height = 750;

    const ctx = canvas.getContext('2d');

    // Draw the template image
    const templateImage = new Image();
    templateImage.src = template;
    templateImage.onload = () => {
      ctx.drawImage(templateImage, 0, 0, 750, 750);

      // Draw the uploaded image at the specified position with cropping and rounded corners
      if (uploadedImage.value) {
        const uploadedImg = new Image();
        uploadedImg.src = uploadedImage.value;
        uploadedImg.onload = () => {
          const aspectRatio = uploadedImg.width / uploadedImg.height;

          // Calculate dimensions for cropping
          let cropWidth, cropHeight, offsetX, offsetY;
          if (aspectRatio > 1) {
            // Landscape (wide) image
            cropWidth = uploadedImg.height;
            cropHeight = uploadedImg.height;
            offsetX = (uploadedImg.width - uploadedImg.height) / 2;
            offsetY = 0;
          } else {
            // Portrait (tall) image
            cropWidth = uploadedImg.width;
            cropHeight = uploadedImg.width;
            offsetX = 0;
            offsetY = (uploadedImg.height - uploadedImg.width) / 2;
          }

          // Draw the cropped image with rounded corners on the right side
          ctx.beginPath();
          ctx.moveTo(canvas.width - 60 - 16, canvas.height - 150 - 260);
          ctx.lineTo(canvas.width - 60 - 260 + 16, canvas.height - 150 - 260);
          ctx.arcTo(canvas.width - 60 - 260, canvas.height - 150 - 260, canvas.width - 60 - 260, canvas.height - 150 - 260 + 16, 16);
          ctx.lineTo(canvas.width - 60 - 260, canvas.height - 150 - 260 + 260 - 16);
          ctx.arcTo(canvas.width - 60 - 260, canvas.height - 150 - 260 + 260, canvas.width - 60 - 260 + 16, canvas.height - 150 - 260 + 260, 16);
          ctx.lineTo(canvas.width - 60 - 16, canvas.height - 150 - 260 + 260);
          ctx.arcTo(canvas.width - 60, canvas.height - 150 - 260 + 260, canvas.width - 60, canvas.height - 150 - 260 + 260 - 16, 16);
          ctx.lineTo(canvas.width - 60, canvas.height - 150 - 260 + 16);
          ctx.arcTo(canvas.width - 60, canvas.height - 150 - 260, canvas.width - 60 - 16, canvas.height - 150 - 260, 16);
          ctx.closePath();

          ctx.clip(); // Apply clipping to create rounded corners
          ctx.drawImage(uploadedImg, offsetX, offsetY, cropWidth, cropHeight, canvas.width - 60 - 260, canvas.height - 150 - 260, 260, 260);

          // Reset the clipping region
          ctx.restore();

          // Trigger the download of the merged image
          const downloadLink = document.createElement('a');
          downloadLink.href = canvas.toDataURL('image/png');
          downloadLink.download = 'merged_image.png';
          downloadLink.click();
        };
      }
    };
  }

  function uploadImage(event) {
    const file = event.target.files[0];

    if (file) {
      const reader = new FileReader();

      reader.onload = () => {
        uploadedImage.value = reader.result;
      };

      reader.readAsDataURL(file);
    }
  }
</script>

<template>
  <main class="mx-auto flex flex-col justify-evenly">
    <div class="bg-[#ff8c2a] py-3">
      <h1 class="text-5xl text-white text-center font-bold text-balance">Genera tu tarjeta de candidato ONE!</h1>
    </div>
    <div class="flex flex-col max-w-[600px] justify-center text-center mx-auto">
      <p class="py-5">
        Sube tu foto y descarga tu tarjeta de candidato ONE!
        <br />
        Puedes publicarla en Linkedin, Facebook e Instagram para mostrar que estás en el proceso de selección del Programa ONE. No olvides utilizar el hashtag
        <span class="text-orange-700">#OracleNextEducation</span>,
        <span class="text-orange-700">#helloONEG6</span>
        y tagear
        <span class="text-orange-700">@Oracle</span>
        y
        <span class="text-orange-700">@AluraLatam</span>
      </p>
      <button @click="$refs.fileInput.click()" class="bg-orange-300 hover:bg-orange-400 border-[1px] border-orange-400 hover:border-orange-500 text-black rounded-lg px-4 py-2 w-64 self-center mt-5">
        Sube tu foto
      </button>
      <button
        v-if="uploadedImage"
        @click="downloadImage"
        class="bg-orange-300 hover:bg-orange-400 border-[1px] border-orange-400 hover:border-orange-500 text-black rounded-lg px-4 py-2 w-64 self-center mt-5">
        Descargar
      </button>
    </div>
    <div class="flex justify-center mt-5">
      <div class="relative w-[750px] h-[750px]">
        <img :src="template" alt="" class="w-[750px] h-[750px] object-contain" />
        <img v-if="uploadedImage" :src="uploadedImage" alt="Uploaded Image" class="absolute right-[60px] bottom-[150px] w-[260px] h-[260px] object-cover rounded-2xl overflow-hidden" />
        <span
          class="absolute right-[60px] bottom-[150px] w-[260px] h-[260px] object-cover rounded-2xl overflow-hidden flex justify-center items-center cursor-pointer"
          @click="$refs.fileInput.click()">
          <span v-if="!uploadedImage" class="text-gray-400 text-2xl">Sube tu foto</span>
        </span>
        <input type="file" @change="uploadImage" accept="image/*" ref="fileInput" class="hidden" />
      </div>
    </div>
    <div>
      <p class="text-center pt-8">
        Hola! puedes ver el repositorio de este proyecto en
        <a href="https://github.com/ralodev/ONE-image-app" class="text-blue-600 font-semibold">GitHub</a>
        <br />
        También te invito a ver mi perfil de
        <a href="https://www.linkedin.com/in/raul-lc/" class="text-blue-600 font-semibold">LinkedIn</a>
      </p>
    </div>
  </main>
</template>
