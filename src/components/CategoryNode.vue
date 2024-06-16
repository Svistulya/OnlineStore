<template>
  <div>
    <ul>
      <div>{{ category.name }}</div>
      <li v-if="category.products.length">
        <ul>
          <li v-for="product in category.products" :key="product.id" :class="{ 'first-item': product.id === category.products[0].id }">
            <input type="checkbox" :value="product.id" @change="selectProduct(product, $event)">
            {{ product.name }} - {{ product.price }} руб.
          </li>
        </ul>
      </li>
      <li v-if="category.categories.length">
        <category-node :category="subcategory" v-for="subcategory in category.categories" :key="subcategory.id" @select-product="selectProduct" />
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'CategoryNode',
  props: {
    category: {
      type: Object,
      required: true
    }
  },
  methods: {
    selectProduct(product, event) {
      this.$emit('select-product', product);
    }
  }
};
</script>

