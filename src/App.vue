<script setup>
  import template from '@/assets/template.jpg';
  import { ref } from 'vue';

  const uploadedImage = ref(null);

  function downloadImage() {
    // Create a canvas to draw the template and uploaded image
    const canvas = document.createElement('canvas');
    // Set sizes to 3x to get a higher resolution image (default is 750x750)
    canvas.width = 750 * 3;
    canvas.height = 750 * 3;
    const selfie_size = 260 * 3;
    const selfie_x = 60 * 3;
    const selfie_y = 150 * 3;
    const border_radius = 16 * 3;

    const ctx = canvas.getContext('2d');

    // Draw the template image
    const templateImage = new Image();
    templateImage.src = template;
    templateImage.onload = () => {
      ctx.drawImage(templateImage, 0, 0, canvas.width, canvas.height);

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
          ctx.moveTo(canvas.width - selfie_x - border_radius, canvas.height - selfie_y - selfie_size);
          ctx.lineTo(canvas.width - selfie_x - selfie_size + border_radius, canvas.height - selfie_y - selfie_size);
          ctx.arcTo(
            canvas.width - selfie_x - selfie_size,
            canvas.height - selfie_y - selfie_size,
            canvas.width - selfie_x - selfie_size,
            canvas.height - selfie_y - selfie_size + border_radius,
            border_radius
          );
          ctx.lineTo(canvas.width - selfie_x - selfie_size, canvas.height - selfie_y - selfie_size + selfie_size - border_radius);
          ctx.arcTo(
            canvas.width - selfie_x - selfie_size,
            canvas.height - selfie_y - selfie_size + selfie_size,
            canvas.width - selfie_x - selfie_size + border_radius,
            canvas.height - selfie_y - selfie_size + selfie_size,
            border_radius
          );
          ctx.lineTo(canvas.width - selfie_x - border_radius, canvas.height - selfie_y - selfie_size + selfie_size);
          ctx.arcTo(
            canvas.width - selfie_x,
            canvas.height - selfie_y - selfie_size + selfie_size,
            canvas.width - selfie_x,
            canvas.height - selfie_y - selfie_size + selfie_size - border_radius,
            border_radius
          );
          ctx.lineTo(canvas.width - selfie_x, canvas.height - selfie_y - selfie_size + border_radius);
          ctx.arcTo(canvas.width - selfie_x, canvas.height - selfie_y - selfie_size, canvas.width - selfie_x - border_radius, canvas.height - selfie_y - selfie_size, border_radius);
          ctx.closePath();

          ctx.clip(); // Apply clipping to create rounded corners
          ctx.drawImage(uploadedImg, offsetX, offsetY, cropWidth, cropHeight, canvas.width - selfie_x - selfie_size, canvas.height - selfie_y - selfie_size, selfie_size, selfie_size);

          // Reset the clipping region
          ctx.restore();

          // Trigger the download of the merged image
          const downloadLink = document.createElement('a');
          downloadLink.href = canvas.toDataURL('image/png');
          downloadLink.download = 'candidato_ONE.png';
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
      <h1 class="text-4xl text-white text-center font-bold text-balance">Tarjeta Oracle Next Education</h1>
    </div>
    <div class="flex flex-col max-w-[500px] text-center justify-center mx-auto">
      <h2 class="pt-5 px-5 text-pretty font-semibold text-lg">Sube tu foto y descarga tu tarjeta de candidato ONE!</h2>
      <p class="pb-5 pt-1 px-5 text-pretty">
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
      <div class="relative template">
        <img :src="template" alt="" class="template object-contain" />
        <img v-if="uploadedImage" :src="uploadedImage" alt="Uploaded Image" class="absolute object-cover overflow-hidden selfie" />
        <span class="absolute object-cover overflow-hidden flex justify-center items-center cursor-pointer selfie" @click="$refs.fileInput.click()">
          <span v-if="!uploadedImage" class="text-gray-400 lg:text-2xl md:text-xl text-xs text-center w-full">Sube tu foto</span>
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
