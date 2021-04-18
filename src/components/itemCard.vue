<template>
  <div class="Outer-Card">
    <div class="product-Image-container">
      <img class="product-Image" :src="require(`../assets/${ImageName}`)" />
      <!-- <img src="..\assets\ShopEz.png" />  -->
    </div>
    <div class="product-text">
      <h2>{{ name }}</h2>
      <h3>Quanity: {{ quanity }}</h3>
      <h3>Unit Price: {{ price.toFixed(2) }}</h3>
    </div>
    <div class="price-text">
      <h2>${{ (price * quanity).toFixed(2) }}</h2>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    name: String,
    price: Number,
    quanity: Number,
    ImageName: String,
  },
  //   data() {
  //     // images: '../assets/' + this.name + '.jpg'
  //   },
  //  computed: {

  //      image: function () {
  //        console.log('../assets/' + this.name + '.jpg')
  //         return '../assets/' + this.name + '.jpg';
  //      }
  //   },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style >
h2 {
  text-align: left;
  font: normal normal 600 25px Myriad Pro;
  letter-spacing: -1.85px;
  color: #7f7f7f;
  margin: 0;
}
h3 {
  text-align: left;
  font: normal normal normal 18px Myriad Pro;
  letter-spacing: -1.51px;
  color: #7f7f7f;
  margin: 0;
}
.Outer-Card {
  background: #ffffff;
  box-shadow: 0px 3px 6px #00000014;
  border-radius: 53px;
  opacity: 1;
  height: 100px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin: 10px 0px;
}
.product-Image-container {
  margin: 0px 10px;
  border: 3px solid #d1bfff;
  border-radius: 50%;
  height: 80px;
  width: 80px;
    display: flex;
  align-items: center;
  justify-content: space-between;
}
.price-text {
  margin: 0px 15px 0px 0px;
}
.product-text {
  width:50%;
}
.product-Image {
  width: 100%;
  border-radius: 50%;
}
</style>
