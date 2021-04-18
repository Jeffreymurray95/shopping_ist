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
    };
  },
  methods: {
    deleteItem: function (index) {
      console.log(index);
      this.foods[index].quanity--;
    },
    addItem: function () {
      var name;
      this.foods[name].quanity++;
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