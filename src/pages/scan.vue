<template>
  <section class="hero is-fullheight has-background-light">
    <div class="hero-body">
      <div class="container">
        <div v-if="scanning" class="has-background-white" style="padding: 20px">
          <no-ssr placeholder="loading...">
            <qrcode-reader @decode="onDecode"/>
          </no-ssr>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
//import AppLogo from '~/components/AppLogo.vue'
import Gun from 'gun/gun'
// import VueQrReader from 'vue-qr-reader/dist/lib/vue-qr-reader.umd.js'

export default {
  components: {},
  data() {
    return {
      tokenName: '',
      recipentAddress: '',
      transferReceipt: '',
      amount: 0,
      itemFalse: false,
      scanning: true
    }
  },
  mounted() {
    this.getAddress()
    this.getBoxes()
    this.$on('codearrived', section => {
      console.log(section)
    })
    setTimeout(function() {
      console.log('hola')
      this.scanning = false
    }, 2000)
  },
  methods: {
    onDecode(decodedString) {
      console.log(decodedString)
      this.addTransporter(decodedString)
    },
    async getAddress() {
      this.tokenName = await this.$store.dispatch(
        'supplyChain/getPackageStrings',
        { index: 0 }
      )
      console.log(await this.tokenName)
    },

    async getBoxes() {
      this.address = await this.$store.dispatch('supplyChain/getAccount')
      var gun = Gun()
      console.log(await this.address)
      gun
        .get(this.address)
        .map()
        .once(function(data, key) {
          console.log(data, key)
        })
    },
    async addTransporter(_index) {
      console.log(_index)
      let packData = await this.$store.dispatch('supplyChain/getPackageData', {
        index: _index
      })

      let transporters = packData[2]
      var counter = 0
      while (
        transporters[counter] != '0x0000000000000000000000000000000000000000' &&
        counter < 10
      )
        counter++

      console.log(counter)
      await this.$store.dispatch('supplyChain/addTransporter', {
        packageNum: _index,
        position: counter
      })
    }
  }
}
</script>

<style scoped>
/*
.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.paragraph-title {
  font-weight: 300;
  font-size: 20px;
  color: #35495e;
  word-spacing: 5px;
  padding: 15px 0;
}

.receipt-box {
  padding-top: 15px;
  width: 900px;
  word-break: break-all;
}

.row {
  padding: 10px;
}

.is-primary {
  border-color: coral;
  background-image: linear-gradient(
    120deg,
    #fdcbf1 0%,
    #fdcbf1 60%,
    #cceeff 100%
  );
}
*/
</style>
