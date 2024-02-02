<template>
  <v-container>
    <v-col justify="center">
    <form @submit.prevent="submit">
      <v-text-field
        v-model="tiktok.value.value"
        :error-messages="tiktok.errorMessage.value"
        label="Veuillez mettre votre lien tiktok"
        class="configText"
      ></v-text-field>
  
      <v-btn
        class="me-4"
        type="submit"
      >
        submit
      </v-btn>
  
      <v-btn @click="handleReset">
        clear
      </v-btn>
      <v-btn @click="sendDataToParent" class="btnSubmit">
        envoie
      </v-btn>
    </form>
  </v-col>
  </v-container>
  </template>

<script setup lang="ts">
import { useField, useForm } from 'vee-validate'
import axios from 'axios'
import cheerio from 'cheerio'
import fs from 'fs'

const loading = true
const emit = defineEmits(['inFocus', 'send-data'])

const { handleSubmit, handleReset } = useForm({
  validationSchema: {
    tiktok (value) {
      if (/^https?:\/\/(?:vm\.|m\.)?tiktok\.com/.test(value)) return true

      return 'Must be a tiktok link valid.'
    },
  },
})
const tiktok = useField('tiktok')
const path = 'C:\Users\Utilisateur\Documents\testAxios'

const submit = handleSubmit(values => {
  downloadTikTokVideo(values, path)
})

function sendDataToParent() {
      // Émettre un événement personnalisé "send-data" avec les données en tant que payload
    emit('send-data', loading);
    }

    async function downloadTikTokVideo(videoUrl: any, outputPath: String) {
    try {
        // Faire une requête HTTP pour récupérer le contenu de la page TikTok
        const response = await axios.get(videoUrl);

        // Utiliser cheerio pour charger le contenu HTML récupéré
        const $ = cheerio.load(response.data);

        // Trouver l'élément vidéo dans la page TikTok
        const videoElement = $('video').first();

        // Extraire l'URL de la vidéo
        const videoSrc = videoElement.attr('src');

        // Télécharger la vidéo en utilisant Axios
        const videoResponse = await axios({
            method: 'get',
            url: videoSrc,
            responseType: 'stream'
        });

        // Créer un flux de sortie pour écrire le contenu de la vidéo dans un fichier
        const writer = fs.createWriteStream(outputPath);

        // Pipe (transférer) le contenu de la réponse vidéo vers le flux de sortie (fichier)
        videoResponse.data.pipe(writer);

        // Résoudre la promesse lorsque l'écriture est terminée
        return new Promise((resolve, reject) => {
            writer.on('finish', resolve);
            writer.on('error', reject);
        });
    } catch (error) {
        console.error('Erreur lors du téléchargement de la vidéo TikTok:', error);
    }
}

</script>
<style scoped>
.btnSubmit {
  background-color: #F0580E;
  color: white;
}
.configText {
  color: white;
}
</style>