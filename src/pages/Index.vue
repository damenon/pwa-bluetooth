<template>
  <q-page class="flex flex-center">
    <q-btn 
      color="primary" 
      @click="onButtonClick"
      label="Teste"
    />
    <textarea readonly 
      rows="3" 
      cols="50" 
      v-model="log"
      style="margin-left: 20px"
    />
  </q-page>
</template>

<style>
</style>

<script>
export default {
  name: 'PageIndex',
  data(){
    return {
      log: ''
    }
  },
  methods: {
    onButtonClick() {
      this.log = 'Requesting Bluetooth Device...';
      navigator.bluetooth.requestDevice({
        acceptAllDevices: true,
        optionalServices: ['battery_service']
      })
      .then(device => {
        this.log.concat('Device: ' + device.name);
        this.log.concat('Connecting to GATT Server...');
        return device.gatt.connect();
      })
      .then(server => {
        this.log.concat('Getting Battery Service...');
        return server.getPrimaryService('battery_service');
      })
      .then(service => {
        this.log.concat('Getting Battery Level Characteristic...');
        return service.getCharacteristic('battery_level');
      })
      .then(characteristic => {
        this.log.concat('Reading Battery Level...');
        return characteristic.readValue();
      })
      .then(value => {
        let batteryLevel = value.getUint8(0);
        this.log.concat('> Battery Level is ' + batteryLevel + '%');
      })
      .catch(error => {
        this.log.concat('ERROR: ' + error);
      });
    }
  }
}
</script>
