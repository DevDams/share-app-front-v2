<template>
  <div class="flex items-center justify-center w-full h-screen">
    <div>
      <h1 class="text-3xl text-center font-medium">Hi Shared</h1>
      <!-- Upload card -->
      <div class="card bg-white shadow-lg rounded-lg border-2 border flex mt-8">
        <!-- left side -->
        <div class="left-side w-1/2 h-full flex items-center justify-center rounded-lg">
          <img src="@/assets/images/Selfie.png" alt="">
        </div>
        <!-- right side -->
        <div class="right-side w-1/2 h-full px-4 py-4 flex flex-col items-center justify-center">
          <!-- Loader -->
          <!-- <div class="loader w-full h-full flex items-center justify-center" :class="load ? 'block' : 'hidden'">
            <img src="@/assets/images/oval.svg" alt="">
          </div>
          <div class="description text-center" :class="load ? 'hidden' : 'block'">
            <p>
              Choisissez une image que vous aimeriez partager, générez un lien de téléchargement et partagez ce lien à vos amis.
            </p>
          </div>
          <div class="form-element text-center mt-10" :class="load ? 'hidden' : 'block'">
            <form action="/api/file" method="post" enctype="multipart/form-data">
              <input type="file" name="file" id="file" class="shadow-md font-medium" />
            </form>
            <button class="gen--link mt-2 bg-rose-500 text-white font-medium text-sm text-center mt-6 px-6 py-3 rounded-xl shadow-md" @click="sendLink">
              Générer un lien de téléchargement
            </button>
          </div> -->
          <!-- Share link -->
          <div class="share relative w-full h-full text-center flex flex-col items-center justify-center">
            <h2 class="font-bold text-2xl mt-4 text-violet-800 uppercase">Votre lien est pret !</h2>
            <div class="copy mt-5 w-full">
              <div class="cancel absolute -top-2 left-5 w-10 h-10 flex items-center justify-center cursor-pointer" @click="cancel">
                <button class="bg-gray-900 text-md text-white px-4 py-1 rounded-lg shadow-lg">Retour</button>
              </div>
              <div class="relative flex items-center w-full">
                <input class="w-full h-12 border-2 border-violet-300 focus:outline-none focus:border-2 focus:border-violet-600 text-md font-normal text-gray-800 pl-4 pr-10 rounded-lg shadow-md" type="text" id="fileURL" placeholder="http://localhost:3000/files/download/5ff3d2fd-6bc3-42c8-b7b4-965d0037034b" readonly @click="copy">
                <img src="~/assets/images/copy.svg" alt="copy icon" class="w-6 absolute right-4" :class="copied ? 'hidden' : 'block'" @click="copy">
                <p class="font-semibold absolute right-4 bg-gray-400 px-2 py-1 rounded-lg text-white" :class="copied ? 'block' : 'hidden'">Copié</p>
              </div>
              <button class="gen--link mt-4 bg-rose-500 text-white font-medium text-sm text-center mt-6 px-6 py-3 rounded-xl shadow-md" @click="shareLink">
                Partager le lien
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import axios from 'axios'
// import * as FilePond from 'filepond'
// import 'filepond/dist/filepond.min.css'
// import FilePondPluginValidateType from 'filepond-plugin-file-validate-type'
export default {
  data() {
    return {
      load: false,
      copied: false
    }
  },
  // mounted() {
  //   // Filepond plugin to validate file type
  //   FilePond.registerPlugin(FilePondPluginValidateType)
  //   // Get a reference to the file input element
  //   const inputElement = document.querySelector('input[type="file"]')
  //   // Create a FilePond instance
  //   const pond = FilePond.create(inputElement, {
  //     labelIdle: 'Glissez ou <span class="filepond--label-action">choisir une image</span>',
  //     acceptedFileTypes: ['image/*'],
  //     onaddfile:(err, file) => {
  //       if (err) {
  //         console.log(err)
  //         this.inputData = ''
  //       } else {
  //         this.inputData = file.file
  //         const error = document.querySelector('.empty-file--message')
  //         error.style.display = 'none'
  //         const genLink = document.querySelector('.gen--link')
  //         genLink.classList.remove('get--link')
  //       }
  //     },
  //     onremovefile: (err, file) => {
  //       if (err) {
  //         console.log(err)
  //         this.inputData = ''
  //         this.alert = 'Veillez choisir une image'
  //       }
  //       this.inputData = ''
  //       this.alert = 'Veillez choisir une image'
  //     }
  //   })
  //   const label = document.querySelector('.filepond--credits')
  //   label.style.display = 'none'
  //   return pond
  // },
  methods: {
    sendLink() {
      this.load = true
    },
    cancel() {
      return 'good'
    },
    copy() {
      return 'copy'
    },
    shareLink() {
      if (navigator.share) {
        navigator
          .share({
            title: "This is header/title",
            text: "This is the description",
            url: "https://put-here-url.com",
          })
          .then(() => console.log("Successful share"))
          .catch((error) => console.log("Error sharing", error));
      } else {
        console.error("Browser doesn't support Web Share API");
      }
    }
  },
}
</script>

<style scoped>
.card {
  width: 800px;
  height: 400px;
}

.left-side {
  background-color: #9a57ff;
}
</style>
