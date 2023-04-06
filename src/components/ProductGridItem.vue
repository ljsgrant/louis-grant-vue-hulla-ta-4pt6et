<template>
  <div class="product-grid-item">
    <img class="product-image" :src="product.image" />
    <div class="product-info-container">
      <p class="product-name">{{ product.name }}</p>
      <p class="product-brand">{{ product.brand }}</p>
      <p class="product-price">£{{ product.price }}</p>
      <p v-if="!product.isAvailable" class="out-of-stock">Out of stock</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductGridItem',
  props: {
    product: { type: Object, required: true, default: () => ({}) },
  },
};
</script>

<style scoped>
.product-grid-item {
  background: #ddebe8;
  max-width: 150px;
  margin: 2%;
  border-radius: 10px;
}

p{
  margin: 2.5px 0px;
}

.product-info-container {
  display: flex;
  flex-direction: column;
  padding: 0px 5px 10px 7px;
}

.out-of-stock {
  color: red;
}

.product-price{
  font-weight: bold;
}

.product-image {
  width: 100%;
  border-radius: 10px 10px 0px 0px;
}
</style>
