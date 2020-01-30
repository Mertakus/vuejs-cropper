<template>
  <div id="app">
    <img
      alt="Vue logo"
      src="./assets/logo.png"
    >

    <div class="wrapper">
      <div class="container">
        <div class="item">
          <input
            type="file"
            ref="fileInput"
            @change="previewImage"
          >
        </div>

        <div class="item cropper-preview">

          <div v-if="!imagePreview">
            <img
              class="input-image"
              src="./assets/placeholder-rounded.png"
              ref="source"
              width="300px"
            >
          </div>
          <div v-else-if="imagePreview">
            <img
              class="input-image"
              :src="imagePreview"
              ref="source"
            >
          </div>
        </div>

        <div class="item button-group">
          <button @click="setupCropper">Start Cropping</button>
          <button @click="cropImage">Confirm</button>
        </div>
      </div>

      <div id="result">
        Result:
        <img
          v-if="showResult"
          :src="imagePreview"
          class="input-image"
        >
      </div>
    </div>
  </div>
</template>

<script>
  import Cropper from "cropperjs";

  export default {
    name: 'app',
    data() {
      return {
        cropper: null,
        croppingProcesStarted: false,
        imagePreview: null,
        showResult: false,
      }
    },
    methods: {
      // getRoundedCanvas(sourceCanvas) {
      //   var canvas = document.createElement("canvas");
      //   var context = canvas.getContext("2d");
      //   var width = sourceCanvas.width;
      //   var height = sourceCanvas.height;
      //   canvas.width = width;
      //   canvas.height = height;
      //   context.imageSmoothingEnabled = true;
      //   context.drawImage(sourceCanvas, 0, 0, width, height);
      //   context.globalCompositeOperation = "destination-in";
      //   this.roundedRect(context, 0, 0, canvas.width, canvas.height, 20);
      //   return canvas;
      // },
      // roundedRect(ctx, x, y, width, height, radius) {
      //   ctx.beginPath();
      //   ctx.moveTo(x, y + radius);
      //   ctx.lineTo(x, y + height - radius);
      //   ctx.arcTo(x, y + height, x + radius, y + height, radius);
      //   ctx.lineTo(x + width - radius, y + height);
      //   ctx.arcTo(x + width, y + height, x + width, y + height - radius, radius);
      //   ctx.lineTo(x + width, y + radius);
      //   ctx.arcTo(x + width, y, x + width - radius, y, radius);
      //   ctx.lineTo(x + radius, y);
      //   ctx.arcTo(x, y, x, y + radius, radius);
      //   ctx.fill();
      // },
      previewImage(event) {
        this.showResult = false;
        // Reference to the DOM input element
        const input = event.target
        let files = input.files

        // const isJPEG = files[0].type === 'image/jpeg';
        // const isJPG = files[0].type === 'image/jpg';
        // const isPNG = files[0].type === 'image/png';
        // const isLt2M = files[0].size / 512 / 512 < 2;


        const reader = new FileReader();
        reader.onload = e => {
          this.imagePreview = e.target.result;
        };
        // Start the reader job - read file as a data url (base64 format)
        reader.readAsDataURL(input.files[0]);
      },
      cropImage() {
        var croppedCanvas;
        var roundedCanvas;
        var roundedImage;
        croppedCanvas = this.cropper.getCroppedCanvas();
        roundedCanvas = croppedCanvas;
        // roundedCanvas = this.getRoundedCanvas(croppedCanvas);
        roundedImage = document.createElement("img");
        roundedImage.src = roundedCanvas.toDataURL("image/png");
        this.imagePreview = roundedImage.src;
        this.showResult = true;
      },
      setupCropper() {
        this.croppingProcesStarted = true;
        if (this.cropper) {
          this.cropper.destroy();
        }

        if (!this.imagePreview) {
          this.cropper = null;
          return;
        }
        this.$nextTick(this.setupCropperInstance);
      },
      setupCropperInstance() {
        this.cropper = new Cropper(this.$refs.source, {
          aspectRatio: 1,
          viewMode: 1,
          // crop: this.debouncedUpdatePreview
        });
      },
    }

  }
</script>

<style>
  #app {
    text-align: center;
  }

  .wrapper {
    display: flex;
    justify-content: center;
  }

  .container {
    display: inline-grid;
    grid-template-columns: 1fr;
    justify-items: center;
    width: 400px;
    height: 600px;
    box-shadow: 0 0 0.5cm rgba(0, 0, 0, 0.35);
  }

  .item {
    padding: 10px;
  }

  .input-image {
    width: 300px;
    padding: 20px;
  }

  #result {
    width: 300px;
  }

  .button-group {
    display: flex;
    align-items: flex-end;
  }
</style>