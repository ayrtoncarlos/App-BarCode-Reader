<template>
  <div class="row items-center" style="height: 100vh">
    <div class="col text-center q-pa-sm">
      <q-btn color="primary" icon="camera_alt" label="Ler código de Barra"
        class="full-width" size="lg" @click="iniciarLeitor()"
        v-show="cameraStatus === 0"/>
      <div class="text-h6" v-if="code">Codigo: {{ code }}</div>
      <div id="scan" v-show="cameraStatus === 1"></div>
      <q-page-sticky position="bottom-right" :offset="[18, 18]">
        <q-btn icon="cancel" color="negative" label="Fechar" v-show="cameraStatus === 1"
        @click="onStop"/>
      </q-page-sticky>
    </div>
  </div>
</template>

<script>
import Quagga from 'quagga'
export default {
  name: 'Leitor',
  data () {
    return {
      code: '',
      dialog: false,
      cameraStatus: 0
    }
  },
  methods: {
    iniciarLeitor () {
      this.cameraStatus = 1
      Quagga.init({
        inputStream: {
          name: 'Live',
          type: 'LiveStream',
          target: document.queryselector('#scan')
        },
        frequency: 10,
        decoder: {
          readers: ['ean_reader'],
          multiple: false
        }
      }, (err) => {
        if (err) {
          console.log(err)
          return
        }
        console.log('Initialization finished. Ready to start')
        Quagga.start()
        Quagga.onDetected(this.onDetected)
      })
    },
    onDetected (data) {
      this.code = data.codeResult.code
      this.cameraStatus = 0
      this.onStop()
    },
    onStop () {
      Quagga.stop()
      this.cameraStatus = 0
    }
  }
}
</script>

<style>
video {
  width: 100%;
  height: auto;
}
canvas {
  display: none;
}
</style>
