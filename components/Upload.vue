<template>
  <div class="flex items-center justify-center w-full h-screen">
    <div class="block-card">
      <h1 class="text-3xl text-center font-medium">Hi Shared</h1>
      <!-- Upload card -->
      <div class="card bg-white shadow-lg rounded-lg border-2 border flex mt-8">
        <!-- left side -->
        <div class="left-side sm:w-1/2 h-full flex items-center justify-center rounded-lg">
          <img src="@/assets/images/Selfie.png" alt="">
        </div>
        <!-- right side -->
        <div class="right-side sm:w-1/2 h-full px-4 py-4 flex flex-col items-center justify-center">
          <!-- Uploader -->
          <div class="description text-center" :class="uploader ? 'block' : 'hidden'">
            <p class="text-lg text-black">
              Choisissez une image que vous aimeriez partager, générez un lien de téléchargement et partagez ce lien à vos amis.
            </p>
          </div>
          <div class="relative form-element text-center mt-8" :class="uploader ? 'block' : 'hidden'">
            <form action="/api/file" method="post" enctype="multipart/form-data">
              <input type="file" name="file" id="file" class="shadow-sm font-medium" />
            </form>
            <button class="gen-link bg-rose-500 text-white font-medium text-sm text-center mt-4 px-6 py-3 rounded-xl shadow-md" @click="sendLink">
              Générer un lien de téléchargement
            </button>
            <p class="empty-file--message absolute w-full left-0 hidden -bottom-8 text-sm text-center text-rose-500 font-bold">{{ alert }}</p>
          </div>
          <!-- Loader -->
          <div class="loader sm:w-full h-full flex items-center justify-center" :class="isLoading ? 'block' : 'hidden'">
            <img src="@/assets/images/oval.svg" alt="">
          </div>
          <!-- Share link -->
          <div class="share relative sm:w-full h-full text-center flex flex-col items-center justify-center" :class="share_ready ? 'block' : 'hidden'">
            <h2 class="font-bold text-2xl mt-4 text-gray-900">Votre lien est pret !</h2>
            <div class="copy mt-5 w-full">
              <div class="cancel absolute -top-1 left-px w-10 h-10 flex items-center justify-center cursor-pointer">
                <button class="bg-rose-500 border-2 border-rose-500 text-lg font-medium text-black w-10 h-10 p-2 rounded shadow-sm flex items-center justify-center" @click="back">
                  <img src="@/assets/images/back.svg" alt="cross icon" class="w-full">
                </button>
              </div>
              <div class="relative flex items-center sm:w-full">
                <input class="w-full h-12 border-2 border-violet-300 focus:outline-none focus:border-2 focus:border-violet-600 text-md font-normal text-gray-800 pl-4 pr-10 rounded-lg shadow-md" type="text" id="fileURL" placeholder="http://localhost:3000/files/download/5ff3d2fd-6bc3-42c8-b7b4-965d0037034b" readonly @click="copy">
                <img src="~/assets/images/copy.svg" alt="copy icon" class="w-6 absolute right-4" :class="copied ? 'hidden' : 'block'" @click="copy">
                <p class="font-semibold absolute right-4 bg-gray-400 px-2 py-1 rounded-lg text-white text-sm" :class="copied ? 'block' : 'hidden'">Copié</p>
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
import axios from 'axios'
import * as FilePond from 'filepond'
import 'filepond/dist/filepond.min.css'
import FilePondPluginValidateType from 'filepond-plugin-file-validate-type'
export default {
  data() {
    return {
      uploader: true,
      isLoading: false,
      copied: false,
      share_ready: false,
      inputData: '',
      alert: '',
      fileId: ''
    }
  },
  mounted() {
    // Filepond plugin to validate file type
    FilePond.registerPlugin(FilePondPluginValidateType)
    // Get a reference to the file input element
    const inputElement = document.querySelector('input[type="file"]')
    // Create a FilePond instance
    const pond = FilePond.create(inputElement, {
      labelIdle: 'Glissez ou <span class="filepond--label-action">choisir une image</span>',
      acceptedFileTypes: ['image/*'],
      onaddfile:(err, file) => {
        if (err) {
          console.log(err)
          this.inputData = ''
        } else {
          this.inputData = file.file
          const error = document.querySelector('.empty-file--message')
          error.style.display = 'none'
          const genLink = document.querySelector('.gen--link')
          genLink.classList.remove('get--link')
        }
      },
      onremovefile: (err, file) => {
        if (err) {
          console.log(err)
          this.inputData = ''
          this.alert = 'Veillez choisir une image'
        }
        this.inputData = ''
        this.alert = 'Veillez choisir une image'
      }
    })
    const label = document.querySelector('.filepond--credits')
    label.style.display = 'none'
    return pond
  },
  methods: {
    async sendLink() {
      try {
        // Check if internet is on
        if (navigator.onLine) {
          const error = document.querySelector('.empty-file--message')
          error.style.display = 'none'
          // Check if a file has been upload
          if (this.inputData === '') {
            this.alert = 'Veillez choisir une image'
            const genLink = document.querySelector('.gen-link')
            genLink.classList.add('get-link')
            setTimeout(() => {
              genLink.classList.remove('get-link')
            }, 300);
            const error = document.querySelector('.empty-file--message')
            error.style.display = 'block'
          } else {
            const error = document.querySelector('.empty-file--message')
            error.style.display = 'none'
            // Save file in database and in cloudinary
            this.uploader = false
            this.isLoading = true
            const formData = new FormData()
            formData.append('file', this.inputData)
            await axios({ method: 'post', url: 'https://i-shared.herokuapp.com/api/add/file', data: formData, headers: { 'Content-Type': 'multipart/form-data', 'Access-Control-Allow-Origin': '*' } }).then(response => {
                if (response.data.file) {
                  this.fileId = response.data.file
                  this.isLoading = false
                  this.share_ready = true
                  const fileURL = document.querySelector('#fileURL')
                  fileURL.value = `https://hi-share.herokuapp.com/doownload/${this.fileId}`
                  this.shareLink = `https://hi-share.herokuapp.com/doownload/${this.fileId}`
                  this.inputData = ''
                }
              }).catch(error => {
                console.log('api error', error)
              })
          }
        } else {
          const error = document.querySelector('.empty-file--message')
          error.style.display = 'block'
          this.alert = 'Vérifiez votre connection internet'
        }
      } catch (error) {
        console.log('error trycatch', error)
      }
    },
    copy() {
      this.copied = true
      setTimeout(() => {
        this.copied = false
      }, 1500)
      const fileURL = document.querySelector('#fileURL')
      fileURL.select()
      document.execCommand("copy")
      if (document.execCommand("copy")) {
        console.log('Copied')
      }
    },
    shareLink() {
      if (navigator.share) {
        navigator
          .share({
            title: "Partage d'image via Hi Shared",
            text: "Hey regarde j'ai ajouté cet image sur Hi shared pour toi",
            url: "https://put-here-url.com",
          })
          .then(() => {
            return 'Successful share'
          })
          .catch((error) => {
            return `Error sharing ${error}`
          });
      } else {
        return "Browser doesn't support Web Share API"
      }
    },
    back() {
      this.share_ready = false
      this.isLoading = false
      const filePondCancel = document.querySelector(".filepond--action-remove-item")
      filePondCancel.click()
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
  background-color: rgb(154, 87, 255);
}

.get-link {
  animation: shake 0.82s cubic-bezier(.36,.07,.19,.97) both;
}

@keyframes shake {
  10%, 90% {
    transform: translate3d(-1px, 0, 0);
  }
  
  20%, 80% {
    transform: translate3d(2px, 0, 0);
  }

  30%, 50%, 70% {
    transform: translate3d(-4px, 0, 0);
  }

  40%, 60% {
    transform: translate3d(4px, 0, 0);
  }
}

@media (max-width: 840px) {
  .card {
    width: 90%;
    height: 400px;
    margin:  30px auto 0;
  }
}

@media (max-width: 639px) {
  .block-card {
    width: 90%;
  }

  .card {
    width: 100%;
    height: 400px;
    margin:  30px auto 0;
  }

  .card .left-side {
    display: none;
  }

  .card .right-side {
    width: 100%;
    /* background: linear-gradient(0deg, rgba(154, 87, 255, 0.6), rgba(154, 87, 255, 0.3)), url('@/assets/images/Selfie.png');
    background-position: center;
    background-repeat: no-repeat;
    background-size: contain;
    border-radius: 8px; */
  }

  .loader, .share {
    width: 100%;
  }
}
</style>
