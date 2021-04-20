<template>
  <div class="home">
    <div class="header">
      <img src="..\assets\ShopEz.png" width="110px" />
    </div>
    <div
      class="indicator"
      :class="[isActive ? 'green' : 'red', Connecting ? 'blink_me' : '']"
      @click="getDeviceInfo()"
    ></div>
    <h1 class="large-text">Current Cart</h1>
    <div v-for="(food, index) in foods" :key="index">
      <transition name="bounce">
        <itemCard
          v-show="food.quanity > 0"
          @click.native="deleteItem(food.id, true)"
          :name="food.name"
          :quanity="food.quanity"
          :price="food.price"
          :ImageName="food.ImageName"
        />
      </transition>
    </div>
    <div class="checkout" @click="getDeviceInfo()">Connect to Bluetooth</div>
    <div class="checkout" @click="connectGATT('RX')">Connect Write</div>
    <div class="checkout" @click="write()">Write</div>
    <div class="checkout" @click="connectGATT('TX')">Connect Read</div>
    <div class="checkout" @click="start()">start</div>
    <div class="checkout" @click="stop()">stop</div>
    <div class="checkout" @click="addItem(333)">add</div>
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
          id: 555,
        },
        {
          name: "Bell Peppers",
          ImageName: "bellpepper.jpg",
          quanity: 3,
          price: 0.5,
          id: 333,
        },
        {
          name: "Bread Loaf",
          ImageName: "bread.jpg",
          quanity: 1,
          price: 3.95,
          id: 222,
        },
      ],
      Connecting: false,
      isActive: false,
      deviceName: "ShopEZ",
      bleService: "6E400001-B5A3-F393-E0A9-E50E24DCCA9E".toLowerCase(),
      bleCharacteristicRX: "6E400002-B5A3-F393-E0A9-E50E24DCCA9E".toLowerCase(),
      bleCharacteristicTX: "6E400003-B5A3-F393-E0A9-E50E24DCCA9E".toLowerCase(),
      bluetoothDeviceDetected: null,
      gattCharacteristic: null,
      itemDelete: 0,
    };
  },
  methods: {
    deleteItem: function (id, sendBack) {
      this.foods[this.foods.findIndex((x) => x.id == id)].quanity--;
      if (sendBack) {
        this.itemDelete = id;
        this.Connecting = true;
        this.connectGATT("RX");
      }
    },
    addItem: function (id) {
      this.foods[this.foods.findIndex((x) => x.id == id)].quanity++;
    },
    getDeviceInfo: function () {
      this.Connecting = true;
      let options = {
        optionalServices: [this.bleService],
        filters: [{ name: this.deviceName }],
      };

      console.log("Requesting any Bluetooth Device...");
      return navigator.bluetooth
        .requestDevice(options)
        .then((device) => {
          this.bluetoothDeviceDetected = device;

          this.connectGATT("TX");
        })
        .catch((error) => {
          console.log("Argh! " + error);
        });
    },
    write: function (id) {
      return (
        Promise.resolve()
          // .then(this.connectGATT())
          .then(() => {
            console.log("Reading UV Index...");
            var enc = new TextEncoder();
            var arr = enc.encode(id);
            // var arr = new Uint8Array([65, 65, 3, 5]);
            return this.gattCharacteristic.writeValueWithoutResponse(arr);
          })
          .then(() => {
            this.connectGATT("TX");
          })
          .catch((error) => {
            this.isActive = true;
            console.log("Waiting to start reading: " + error);
          })
      );
    },

    connectGATT: function (charType) {
      this.isActive = true;
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
            this.start();
            this.gattCharacteristicTX = characteristic;
            this.gattCharacteristicTX.addEventListener(
              "characteristicvaluechanged",
              this.handleChangedValue
            );
          } else {
            this.gattCharacteristicRX = characteristic;
            this.write("-" + this.itemDelete);
          }
        });
    },
    handleChangedValue: function (event) {
      let id = String.fromCharCode.apply(
        null,
        new Uint8Array(event.target.value.buffer)
      );
      // let value = event.target.value.Uint8Array(0);
      if (id[0] == "-") {
        this.deleteItem(id.substring(1), false);
      } else {
        this.addItem(id.substring(1));
      }
      console.log(id);
    },
    start: function () {
      this.gattCharacteristicTX
        .startNotifications()
        .then(() => {
          console.log("Start reading...");
          this.Connecting = false;
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
.indicator {
  transform: translateY(-42.5px);
  width: 10px;
  height: 10px;
  animation: all 0.3s;
  border-radius: 50%;
  transition: all 0.2s;
  margin-left: 25px;
}

.red {
  background-color: red;
  box-shadow: 0px 0px 10px red;
  animation: all 0.3s;
}
.green {
  background-color: green;
  box-shadow: 0px 0px 10px green;
  animation: all 0.3s;
}
.blink_me {
  animation: blinker 1s linear infinite;
}

@keyframes blinker {
  50% {
    opacity: 0;
  }
}
.bounce-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
</style>