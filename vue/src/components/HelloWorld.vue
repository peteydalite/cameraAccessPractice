<template>
  <div class="hello">
    <form>
      <div id='barcode-scanner'></div>
      <div v-if='isQuagga'>
        <button  v-on:click.prevent='display'>Camera On</button>
        <button  v-on:click.prevent='close'>Camera Off</button>
      </div>
      <div v-else>
        <input type="file" accept="video/*;capture=camcorder" v-on:change="intakePic($event)">
        <button :disabled="!ready" type="submit" v-on:click.prevent="submitBarcode()">submit</button>
      </div>
    </form>
    <div>{{QuaggaResult}}</div>
  </div>
</template>

<script>
import Quagga from 'quagga'; // ES6

// const Quagga = require('quagga').default; // Common JS (important: default)

export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      showMessage: false,
      isQuagga: true,
      ready: false,
      data_url: '',
      QuaggaResult: '',
      scanned: false,
    }
  },
  methods: {
    display () {
      if (navigator.mediaDevices && typeof navigator.mediaDevices.getUserMedia === 'function') {
      // safely access `navigator.mediaDevices.getUserMedia`
        // Quagga.init({
        //   inputStream : {
        //   name : "Live",
        //   type : "LiveStream",
        //   target: document.querySelector('#barcode-scanner')    // Or '#yourElement' (optional)
        // },
        //   decoder : {
        //     readers : ['ean_reader', 'ean_8_reader', 'code_39_reader', 'code_39_vin_reader', 'codabar_reader', 'upc_reader', 'upc_e_reader', 'i2of5_reader', '2of5_reader', 'code_93_reader']
        //   }
        // }, function(err) {
        //     if (err) {
        //       console.log(err);
        //     return
        // }
        //   console.log("Initialization finished. Ready to start");
        //   Quagga.start();
        // });

        //testing for ios
        this.isQuagga = false;
      }else{
        window.alert('Error. Please upload barcode image instead');
        this.isQuagga = false;
      }

    },
    close(){
      Quagga.stop();
      console.log('camera off')
    },
    intakePic(event){
      console.log(event);
      const fileName = event.target.files[0];

      const reader = new FileReader();
      reader.readAsDataURL(fileName);
      
      console.log(reader);

      reader.onload = () => {
        this.data_url = reader.result;
        this.ready = true;
      };
    },
    submitBarcode(event) {
      Quagga.decodeSingle({
        decoder: {
          readers : ['ean_reader', 'ean_8_reader', 'code_39_reader', 'code_39_vin_reader', 'codabar_reader', 'upc_reader', 'upc_e_reader', 'i2of5_reader', '2of5_reader', 'code_93_reader'] // List of active readers
        },
        locate: true, // try to locate the barcode in the image
        src: this.data_url //'data:image/jpg;base64,'+base64Data // or 'data:image/jpg;base64,' + data
      },function(result){
        if(result) {
          if(result.codeResult) {
            window.alert("result " + result.codeResult.code);
          } 
      }else{
          window.alert("Unscanned successfully!");
          console.log(result);
          
      } 
      });

      if(this.scanned){
        this.QuaggaResult = "Successfully scanned!"
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
