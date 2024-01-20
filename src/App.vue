<script setup>
  import template from '@/assets/template.jpg';
  import TopBar from './components/TopBar.vue';
  import UploadIcon from '@/components/icons/UploadIcon.vue';
  import DownloadIcon from '@/components/icons/DownloadIcon.vue';
  import bg_naranja from '@/assets/bg_naranja.jpg';
  import bg_verde from '@/assets/bg_verde.jpg';
  import bg_verde2 from '@/assets/bg_verde2.jpg';
  import bg_cafe from '@/assets/bg_cafe.jpg';

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

  function tryDownloadImage() {
    if (uploadedImage.value) {
      downloadImage();
    } else {
      alert('Por favor, sube una imagen primero');
    }
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
  <TopBar class="shadow-lg z-50" />
  <main class="mx-auto flex flex-col justify-evenly mt-[100px] text-[#713e24]">
    <div class="py-2">
      <h1 class="text-4xl text-center font-bold text-balance">¡Bievenido candidato ONE!</h1>
    </div>
    <!-- Imagen y plantilla -->
    <div class="flex justify-center mx-auto">
      <h2 class="pt-5 px-5 text-pretty font-semibold text-xl">Ponemos a tu disposición esta plantilla para que puedas compartir tu postulación en redes sociales.</h2>
    </div>
    <div class="flex flex-col sm:flex-row w-full justify-center gap-10 my-5 px-5 rounded-xl bg-[#fdd0b2] text-xl font-semibold">
      <div class="flex flex-col max-w-[500px] my-auto gap-5">
        <p class="text-pretty text-lg pt-5 sm:pt-0">
          Puedes publicarla en Linkedin, Facebook e Instagram para mostrar que estás en el proceso de selección del Programa ONE.
          <br />
          No olvides utilizar el hashtag
          <span class="text-blue-800">#OracleNextEducation</span>,
          <span class="text-blue-800">#helloONEG6</span>
          y tagear a
          <span class="text-blue-800">@Oracle</span>
          y
          <span class="text-blue-800">@AluraLatam</span>
        </p>
        <div class="flex gap-5">
          <button
            @click="$refs.fileInput.click()"
            class="inline-flex items-center gap-1 px-4 py-2 w-36 justify-center bg-orange-600 hover:bg-orange-500 border-2 border-orange-700 hover:border-orange-600 text-white text-sm font-medium rounded-md duration-100">
            <UploadIcon />
            Sube tu foto
          </button>
          <button
            @click="tryDownloadImage"
            class="inline-flex items-center gap-1 px-4 py-2 w-36 justify-center text-white text-sm font-medium rounded-md duration-100"
            :class="
              uploadedImage ? 'cursor-pointer bg-orange-600 hover:bg-orange-500 border-2 border-orange-700 hover:border-orange-600' : 'cursor-not-allowed bg-gray-500 border-[2px] border-gray-600'
            ">
            <DownloadIcon />
            Descargar
          </button>
        </div>
      </div>
      <div class="flex flex-col text-center justify-center pb-10">
        <p class="pb-2 pt-3">Previsualización</p>
        <div class="relative template mx-auto">
          <img :src="template" alt="" class="template object-contain" />
          <img v-if="uploadedImage" :src="uploadedImage" alt="Uploaded Image" class="absolute object-cover overflow-hidden selfie" />
          <span class="absolute object-cover overflow-hidden flex justify-center items-center cursor-pointer selfie" @click="$refs.fileInput.click()">
            <span v-if="!uploadedImage" class="text-gray-400 lg:text-2xl md:text-xl text-xs text-center w-full">Sube tu foto</span>
          </span>
          <input type="file" @change="uploadImage" accept="image/*" ref="fileInput" class="hidden" />
        </div>
      </div>
    </div>
    <!-- Fondos de pantalla -->
    <div class="flex justify-center mx-auto">
      <h2 class="pt-5 px-5 text-pretty font-semibold text-xl">Además, puedes descargar los fondos de pantalla que @Oracle y @AluraLatam han preparado para ti.</h2>
    </div>
    <!-- Previsualización -->
    <div class="w-full grid grid-cols-2 md:grid-cols-4 justify-center my-5 gap-5 p-5 rounded-xl bg-[#fdd0b2] text-xl font-semibold text-center">
      <a href="https://caelum-online-public.s3.amazonaws.com/oracle-one-fase2/one-lad-kit-finalizacion/BG_ZOOM_ONE_ESP_%282%29%5B1%5D.png" target="_blank">
        <img :src="bg_naranja" alt="" class="object-cover w-full rounded-xl" />
        <p>Naranja</p>
      </a>
      <a href="https://caelum-online-public.s3.amazonaws.com/oracle-one-fase2/one-lad-kit-finalizacion/BG_ZOOM_ONE_ESP_%281%29%5B1%5D.png" target="_blank">
        <img :src="bg_verde" alt="" class="object-cover w-full rounded-xl" />
        <p>Azul</p>
      </a>
      <a href="https://caelum-online-public.s3.amazonaws.com/oracle-one-fase2/one-lad-kit-finalizacion/BG_ZOOM_ONE_ESP_%283%29%5B1%5D.png" target="_blank">
        <img :src="bg_verde2" alt="" class="object-cover w-full rounded-xl" />
        <p>Verde</p>
      </a>
      <a href="https://caelum-online-public.s3.amazonaws.com/oracle-one-fase2/one-lad-kit-finalizacion/BG_ZOOM_ONE_ESP_%284%29%5B1%5D.png" target="_blank">
        <img :src="bg_cafe" alt="" class="object-cover w-full rounded-xl" />
        <p>Marrón</p>
      </a>
    </div>
    <!-- Pie de página -->
    <div>
      <p class="text-center py-8 bg-[#713e24] text-white text-sm">
        Hecho con ❤️ por <a href="https://ralo.dev/" target="_blank" class="text-blue-200 bg-[#5e311b] rounded-lg px-1 font-semibold">@ralodev</a>, puedes ver el código fuente de este proyecto en
        <a href="https://github.com/ralodev/ONE-image-app" target="_blank" class="text-blue-300 bg-[#5e311b] rounded-lg px-1 font-semibold">GitHub</a>
        <br />
        Las imágenes utilizadas en este proyecto son propiedad de Oracle y Alura. Este proyecto fue hecho con fines educativos y no tiene ningún fin comercial.
      </p>
    </div>
  </main>
</template>
