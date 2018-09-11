<template>
  <q-page class="flex flex-center">
    <img alt="Quasar logo" src="~assets/quasar-logo-full.svg">
    <button type="button" @click="onButtonClick">Teste</button>
  </q-page>
</template>

<style>
</style>

<script>
export default {
  name: 'PageIndex',
  methods: {
    onButtonClick() {
      console.log('Requesting Bluetooth Device...');
      navigator.bluetooth.requestDevice(
        {filters: [{services: ['battery_service']}]})
      .then(device => {
        console.log('Connecting to GATT Server...');
        return device.gatt.connect();
      })
      .then(server => {
        console.log('Getting Battery Service...');
        return server.getPrimaryService('battery_service');
      })
      .then(service => {
        console.log('Getting Battery Level Characteristic...');
        return service.getCharacteristic('battery_level');
      })
      .then(characteristic => {
        console.log('Reading Battery Level...');
        return characteristic.readValue();
      })
      .then(value => {
        let batteryLevel = value.getUint8(0);
        console.log('> Battery Level is ' + batteryLevel + '%');
      })
      .catch(error => {
        console.log('Argh! ' + error);
      });
    }
  }
}
</script>
