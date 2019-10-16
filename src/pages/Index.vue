<template>
  <div class="row items-center" style="height: 100vh">
    <div class="col text-center q-pa-sm ">
      <q-select v-show="cameraStatus === 0" outlined v-model="reader" :options="options" label="tipo" />
      <q-btn color="primary" icon="camera_alt" label="Ler Código de Barras"
        class="full-width" size="lg" @click="iniciarLeitor()"
        v-show="cameraStatus === 0"/>
      <div class="text-h6" v-if="code">Codigo: {{ code }}</div>
      <div id="scan" v-show="cameraStatus === 1"></div>
      <q-page-sticky position="bottom-right" :offset="[18, 18]">
        <q-btn  icon="cancel" color="negative" label="Fechar" v-show="cameraStatus === 1"
        @click="onStop" />
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
      cameraStatus: 0,
      options: [
        'code_128_reader',
        'ean_reader',
        'ean_8_reader',
        'code_39_reader',
        'code_39_vin_reader',
        'codabar_reader',
        'upc_reader',
        'upc_e_reader',
        'i2of5_reader',
        '2of5_reader',
        'code_93_reader'
      ],
      reader: ''
    }
  },
  methods: {
    iniciarLeitor () {
      this.cameraStatus = 1
      Quagga.init({
        inputStream: {
          name: 'Live',
          type: 'LiveStream',
          // constraints: {
          //   width: 300,
          //   height: 300
          // },
          target: document.querySelector('#scan')
        },
        frequency: 10,
        decoder: {
          readers: [
            this.reader
          ],
          multiple: false
        },
        numOfWorkers: navigator.hardwareConcurrency
        // locate: false
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
/* #scan {
  border-right: 15vh solid orange;
  border-left: 15vh solid orange;
  height: 70vh
} */
/* .verticalLine {
  border-left: thick solid #ff0000;
} */
</style>
