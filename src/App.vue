<template>
  <div class="app" ref="app-container">
    <div class="task">
      <h2>Welcome to the Hullabalook Technical Assessment</h2>
      <p>
        You are provided with a set of products and are required to create a
        products listing page for a footwear retailer.<br />
        Write functionality and styling to:
      </p>
      <ol>
        <li>Lay out all products in a responsive product grid</li>
        <li>Create a filter toggle that shows only available products</li>
        <li>
          Create a checkbox brand filter that shows only toggled brand products
        </li>
        <li>Add a counter for the number of resulting products</li>
        <li>
          Create a dropdown to sort all products into ascending or descending
          price order
        </li>
        <li>
          Add an option to sort all products by relevance - with all available
          products shown first in ascending rank order, then all unavailable
          products in ascending rank order.
        </li>
      </ol>
      <p>
        You will be assessed on both behaviour and design. Don't spend more than
        2 hours on this.
      </p>

      <h3>Instructions</h3>
      <p>
        To start, fork
        <a
          class="link"
          href="https://stackblitz.com/edit/vue-hulla-ta"
          target="_blank"
          >this</a
        >
        project and set the project name to include your name. When you're
        finished, connect this to your GitHub account, push the changes and
        email the links to the repo and this project back to us.
        <br />
        If you wish to continue past the allocated time, please create another
        fork.
      </p>
      <hr />
    </div>

    <div class="products-container">
      <div class="filters">
        <h3>Filters</h3>
        <div class="single-filter-wrapper">
          <label for="sort-by">Sort By:</label>
          <select @change="handleSortChange" name="sort-by" id="sort-by">
            <option value="">None</option>
            <option value="ascending">Lowest Price First</option>
            <option value="descending">Highest Price First</option>
            <option value="relevance">Relevance</option>
          </select>
        </div>
        <div class="single-filter-wrapper">
          <label>
            <input v-model="areProductsFilteredByStock" type="checkbox" />
            Show only products that are in stock
          </label>
        </div>
        <div class="single-filter-wrapper">
          <p>Show products by brand:</p>
          <div v-for="brand in brandList">
            <label>
              <input
                type="checkbox"
                v-bind:value="brand"
                @change="handleBrandFilter"
              />
              {{ brand }}
            </label>
          </div>
        </div>
      </div>

      <div class="product-grid-wrapper">
        <div class="product-counter">
          <p>
            Showing {{ filteredProducts.length }} of
            {{ products.length }} products
          </p>
        </div>
        <hr />
        <div class="product-grid">
          <ProductGridItem
            v-for="product in filteredProducts"
            :product="product"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ProductGridItem from './components/ProductGridItem.vue';
import products from './data/products.json';

export default {
  name: 'App',
  components: {
    ProductGridItem,
  },
  data() {
    return {
      products,
      productsSortedBy: null,
      areProductsFilteredByStock: false,
      brandsToFilterBy: [],
    };
  },
  methods: {
    handleSortChange(event) {
      this.productsSortedBy = event.target.value;
    },
    handleBrandFilter(event) {
      if (event.target.checked) {
        this.brandsToFilterBy.push(event.target.value);
      } else {
        const index = this.brandsToFilterBy.indexOf(event.target.value);
        this.brandsToFilterBy.splice(index, 1);
      }
    },
  },
  computed: {
    brandList() {
      const allBrandsList = this.products.map((product) => product.brand);
      const uniqueBrandList = [...new Set(allBrandsList)];
      return uniqueBrandList;
    },
    filteredProducts() {
      let filteredProducts = [...this.products];
      // sort products in ascending/descending order
      if (this.productsSortedBy === 'ascending') {
        filteredProducts.sort((a, b) => {
          if (a.price < b.price) {
            return -1;
          } else if (a.price > b.price) {
            return 1;
          } else {
            return 0;
          }
        });
      } else if (this.productsSortedBy === 'descending') {
        filteredProducts.sort((a, b) => {
          if (a.price > b.price) {
            return -1;
          } else if (a.price < b.price) {
            return 1;
          } else {
            return 0;
          }
        });
      } else if (this.productsSortedBy === 'relevance') {
        filteredProducts.sort((a, b) => {
          // sort by availability
          if (a.isAvailable && !b.isAvailable) {
            return -1;
          } else if (!a.isAvailable && b.isAvailable) {
            return 1;
            // then if both available or both unavailable, sort by rank
          } else {
            if (a.rank === null) {
              return 1;
            } else if (b.rank === null) {
              return -1;
            } else if (a.rank < b.rank) {
              return -1;
            } else if (a.rank > b.rank) {
              return 1;
            } else {
              return 0;
            }
          }
        });
      }
      // filter by if item is in stock
      if (this.areProductsFilteredByStock) {
        filteredProducts = filteredProducts.filter(
          (product) => product.isAvailable
        );
      }
      // filtered by brand
      if (this.brandsToFilterBy.length) {
        filteredProducts = filteredProducts.filter((product) => {
          return this.brandsToFilterBy.includes(product.brand);
        });
      }
      return filteredProducts;
    },
  },
};
</script>

<style>
.app {
  max-width: 1024px;
  margin: 0 auto;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  color: #606569;
}

.link {
  color: #3a7f71;
}

.products-container {
  display: flex;
}

@media only screen and (max-width: 600px) {
  .products-container {
    flex-direction: column;
  }
}

.product-grid {
  display: flex;
  flex-wrap: wrap;
}

.product-grid-wrapper {
  flex-basis: 80%;
  padding-left: 1%;
}

.product-counter,
.product-grid-wrapper > hr {
  margin-left: 2%;
  margin-right: 2%;
}

.filters {
  min-height: 100%;
  background-color: #f0f0f0;
  padding: 0px 5px;
}

.single-filter-wrapper {
  margin: 1px 0px;
  border-top: 1px solid grey;
  padding: 5px 0px;
}

select {
  margin-left: 5px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  color: #606569;
  width: 70%;
}
</style>
