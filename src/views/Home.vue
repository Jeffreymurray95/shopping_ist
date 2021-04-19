<template>
  <div class="home">
    <div class="header">
      <img src="..\assets\ShopEz.png" width="110px" />
    </div>
    <h1 class="large-text">Current Cart</h1>
    <div v-for="(food, index) in foods" :key="index">
      <itemCard
        v-show="food.quanity > 0"
        @click.native="deleteItem(index)"
        :name="food.name"
        :quanity="food.quanity"
        :price="food.price"
        :ImageName="food.ImageName"
      />
    </div>
    <div class="checkout" @click="getDeviceInfo()">Connect to Bluetooth</div>
    <div class="checkout" @click="connectGATT('RX')">Connect Write</div>
    <div class="checkout" @click="write()">Write</div>
    <div class="checkout" @click="connectGATT('TX')">Connect Read</div>
    <div class="checkout" @click="start()">Read</div>
    <div class="checkout" @click="stop()">Read</div>
    <payNow :total="this.totalPrice" />
  </div>
</template>

<script>
// @ is an alias to /src
import itemCard from "@/components/itemCard.vue";
import payNow from "@/components/payNow.vue";

export default {
  name: "Home",
  components: {
    itemCard,
    payNow,
  },
  data: function () {
    return {
      foods: [
        {
          name: "Bananas",
          ImageName: "Bananas.jpg",
          quanity: 5,
          price: 1.5,
        },
        {
          name: "Bell Peppers",
          ImageName: "bellpepper.jpg",
          quanity: 3,
          price: 0.5,
        },
        {
          name: "Bread Loaf",
          ImageName: "bread.jpg",
          quanity: 1,
          price: 3.95,
        },
      ],
      deviceName: "ShopEZ",
      bleService: "6E400001-B5A3-F393-E0A9-E50E24DCCA9E".toLowerCase(),
      bleCharacteristicRX: "6E400002-B5A3-F393-E0A9-E50E24DCCA9E".toLowerCase(),
      bleCharacteristicTX: "6E400003-B5A3-F393-E0A9-E50E24DCCA9E".toLowerCase(),
      bluetoothDeviceDetected: null,
      gattCharacteristic: null,
    };
  },
  methods: {
    deleteItem: function (index) {
      console.log(index);
      this.foods[index].quanity--;
    },
    addItem: function (foodId) {
      var name;
      this.foods[name].quanity++;
    },
    getDeviceInfo: function () {
      let options = {
        optionalServices: [this.bleService],
        filters: [{ name: this.deviceName }],
      };

      console.log("Requesting any Bluetooth Device...");
      return navigator.bluetooth
        .requestDevice(options)
        .then((device) => {
          this.bluetoothDeviceDetected = device;
        })
        .catch((error) => {
          console.log("Argh! " + error);
        });
    },
    write: function () {
      return (
        Promise.resolve()
          // .then(this.connectGATT())
          .then(() => {
            console.log("Reading UV Index...");
            var arr = new Uint8Array([65, 65, 3, 5]);
            return this.gattCharacteristic.writeValueWithoutResponse(arr);
          })
          .catch((error) => {
            console.log("Waiting to start reading: " + error);
          })
      );
    },

    connectGATT: function (charType) {
      if (
        this.bluetoothDeviceDetected.gatt.connected &&
        this.gattCharacteristic
      ) {
        return Promise.resolve();
      }
      return this.bluetoothDeviceDetected.gatt
        .connect()
        .then((server) => {
          console.log("Getting GATT Service...");
          return server.getPrimaryService(this.bleService);
        })
        .then((service) => {
          console.log("Getting GATT Characteristic...");
          if (charType == "TX") {
            return service.getCharacteristic(this.bleCharacteristicTX);
          } else {
            return service.getCharacteristic(this.bleCharacteristicTX);
          }
        })
        .then((characteristic) => {
          if (charType == "TX") {
          this.gattCharacteristicTX = characteristic;
          this.gattCharacteristicTX.addEventListener(
            "characteristicvaluechanged",
            this.handleChangedValue
          );
          }
          else{
              this.gattCharacteristicRX = characteristic;
          }
        });
    },
    handleChangedValue: function (event) {
      let value = String.fromCharCode.apply(
        null,
        new Uint8Array(event.target.value.buffer)
      );
      // let value = event.target.value.Uint8Array(0);
      console.log(value);
    },
    start: function () {
      this.gattCharacteristicTX
        .startNotifications()
        .then(() => {
          console.log("Start reading...");
        })
        .catch((error) => {
          console.log("[ERROR] Start: " + error);
        });
    },

    stop: function () {
      this.gattCharacteristicTX
        .stopNotifications()
        .then(() => {
          console.log("Stop reading...");
        })
        .catch((error) => {
          console.log("[ERROR] Stop: " + error);
        });
    },
  },
  computed: {
    totalPrice() {
      var total = 0;
      for (const index in this.foods) {
        total += this.foods[index].price * this.foods[index].quanity;
      }
      console.log(total);
      return total;
    },
  },
};
</script>
<style >
.header {
  display: flex;
  align-items: center;
  justify-content: center;

  height: 75px;
  width: 100%;
  box-shadow: 0px 3px 6px #c0c0c0;
}
.large-text {
  text-align: left;
  font: normal bold 55px Myriad Pro;
  letter-spacing: -3px;
  line-height: 050px;
  color: #d1bfff;
  margin: 30px 0px 0px 5px;
}
.home {
}
</style>