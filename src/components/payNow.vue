<template>
  <div class="bottom-Bar">
    <div class="total">
      <h4>${{total.toFixed(2)}}</h4>
    </div>
    <div class="checkout">
      <h4>Checkout</h4>
    </div>
  </div>
</template>

<script>
export default {
  name: "payNow",
  props: {
    total: Number,
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

h4 {
  text-align: center;
  font: normal normal bold 35px Myriad Pro;
  letter-spacing: -1.07px;
line-height: 0;
  margin: 0;
}
.bottom-Bar {
  height: 90px;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  position: fixed;
  bottom: 20px;
  left: 0;
}

.checkout {
  background: #a489e6 0% 0%;
  box-shadow: 0px 3px 6px #00000029;
  border-radius: 53px;
  width: 48%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #ffffff;
  margin: 5px;
}
.total {
  background: #ffffff 0% 0% no-repeat padding-box;
  box-shadow: 0px 3px 6px #00000029;
  border-radius: 53px;
  width: 48%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #a489e6;
  margin: 5px;

}
</style>
