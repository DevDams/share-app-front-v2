<template>
  <div class="flex items-center justify-center w-full h-screen">
    <div class="block-card">
      <h1 class="text-3xl text-center font-medium">Hi Shared</h1>
      <!-- Upload card -->
      <div class="card bg-white shadow-lg rounded-lg border-2 border flex mt-8">
        <!-- left side -->
        <div class="left-side sm:w-1/2 h-full flex items-center justify-center rounded-lg">
          <img :src="preview" alt="preview image" class="object-cover w-full h-full rounded-lg">
        </div>
        <!-- right side -->
        <div class="right-side bg-sky-50 sm:w-1/2 h-full px-4 py-4 flex flex-col items-center justify-center">
          <!-- Uploader -->
          <div class="description text-center">
            <p class="text-lg text-black">
              Votre image est prete à etre télécharger. Commencez le téléchargement quand vous voulez.
            </p>
          </div>
          <div class="relative form-element text-center mt-8">
            <p class="text-xl font-bold text-center mt-3">{{ file.fileName }}</p>
            <p v-if="file" class="text-lg font-medium text-center">{{ parseInt(file.fileSize/1000) }} KB</p>
            <button class="gen-link bg-rose-500 text-white font-medium text-sm text-center mt-4 px-6 py-3 rounded-xl shadow-md">
              Télécharger !
            </button>
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
      preview: ''
    }
  },
  async mounted() {
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
    })
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

.left-side {
  background-color: rgb(154, 87, 255);
}
</style>