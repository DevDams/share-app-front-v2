<template>
  <div class="flex items-center justify-center w-full h-screen">
    <!-- Download card -->
    <div class="block-card">
      <h1 class="text-3xl text-center font-medium">Hi Shared</h1>
      <!-- Upload card -->
      <div class="card bg-white shadow-lg rounded-lg border-2 border flex flex-col mt-8 sm:flex sm:flex-row">
        <!-- left side -->
        <div class="left-side relative bg-gray-50 shadow-sm sm:shadow sm:w-1/2 h-full flex items-center justify-center rounded-lg">
          <img v-if="preview" :src="preview" alt="preview image" class="absolute object-cover sm:w-full h-full">
          <div class="overlay absolute top-0 left-0 w-full h-full rounded-xl flex items-center justify-center">
          </div>
        </div>
        <!-- right side -->
        <div class="right-side w-full sm:w-1/2 h-full px-4 py-4 flex flex-col items-center justify-center">
          <!-- Uploader -->
          <div class="description text-center">
            <h2 class="font-bold text-xl mt-4 text-gray-900">Votre image est prete à etre télécharger.</h2>
            <p class="text-md text-black mt-2">
              Commencez le téléchargement quand vous voulez.
            </p>
          </div>
          <div class="relative form-element text-center mt-4">
            <p v-if="file" class="text-md font-medium text-center">Taille: {{ parseInt(file.fileSize/1000) }} KB</p>
            <div class="flex flex-col justify-center">
              <button class="gen-link bg-rose-500 text-white font-medium text-sm text-center mt-2 sm:mt-4 px-6 py-3 rounded-xl shadow-md" @click="download(file)">
                Télécharger !
              </button>
              <nuxt-link to="/" class="mt-3">
                <button class="text-center text-violet-500 font-bold uppercase">Accueil</button>
              </nuxt-link>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      file: '',
      fileExtension: '',
      preview: '',
      visible_preview: false
    }
  },
  async mounted() {
    try {
      const config = {
        headers: {'Access-Control-Allow-Origin': '*'}
      }
      await axios({
        url: `https://i-shared.herokuapp.com/api/files/${this.$route.params.id}`,
        method: 'GET',
        config
      }).then(response => {
        this.file = response.data
        this.preview = response.data.downloadLink
        const lastDot = this.file.fileName.lastIndexOf('.')

        this.fileExtension = this.file.fileName.substring(0, lastDot)
      }).catch(error => {
        console.log('api error', error)
      })
    } catch (error) {
      console.log('trycatch error', error)
    }
  },
  methods: {
    download (file) {
      axios({
        url: `${this.file.downloadLink}`,
        method: 'GET',
        responseType: 'blob'
      }).then(response => {
        const url = window.URL.createObjectURL(new Blob([response.data], { type: response.data.type }))
        console.log(response.data)
        const link = document.createElement('a')
        link.href = url
        link.setAttribute("download", `${this.fileExtension}`)
        document.body.appendChild(link)
        link.click()
      })
    },
    show() {
      this.visible_preview = !this.visible_preview
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Readex+Pro:wght@300;400;500;600;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background-image: url('@/assets/images/hero.jpg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  width: 100%;
  font-family: 'Readex Pro', sans-serif;
}

.card {
  width: 800px;
  height: 400px;
}

.left-side .overlay {
  background: rgba(0, 0, 0, 0.3);
}

.imge-preview {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 99;
}

.preview-overlay {
  background: rgba(0, 0, 0, 0.3);
  width: 100vw;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 90;
}

@media (max-width: 840px) {
  .block-card {
    width: 90%;
  }

  .card {
    width: 100%;
    margin:  30px auto 0;
  }
}

@media (max-width: 639px) {
  .card {
    height: 500px;
  }
}

@media (max-width: 492px) {
  .card {
    height: 550px;
  }
}
</style>