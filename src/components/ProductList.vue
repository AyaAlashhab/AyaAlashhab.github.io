<template>
  <div>
    <div class="row">
      <button class="btn btn-outline-secondary mr-2" style="height: 33px;" @click="toggleFilters">
        <img src="@/assets/img/icon/filter.png" style="height: 18px; margin-right: 5px;" alt="">Hide Filters
      </button>
      <div class="shop__selected-filters">
        <!-- Display selected filters -->
        <div v-for="(selectedFilter, index) in selectedFilters" :key="index" class="selected-filter">
          {{ selectedFilter }}
          <span @click="removeFilter(index)" class="close-icon">x</span>
        </div>
      </div>
      <div class="listed-products-label">
        | {{ products.length }} items
      </div>
    </div>

    <div class="row">
      <div class="col-xs-6 col-lg-4 col-md-6 col-sm-6" v-for="product in products" :key="product.id">
        <div class="product__item">
          <div class="product__item__pic set-bg"
            :style="{ backgroundImage: 'url(' + product.default_variant.image + ')' }">
            <ul class="product__hover">
              <li><a href="#"><img src="img/icon/heart.png" alt=""></a></li>
              <li><a href="#"><img src="img/icon/compare.png" alt=""> <span>Compare</span></a></li>
              <li><a href="#"><img src="img/icon/search.png" alt=""></a></li>
            </ul>
          </div>
          <div class="product__item__text">
            <h6>{{ product.default_variant.product_title }}</h6>
            <a href="#" class="add-cart">+ Add To Cart</a>
            <h5>{{ product.default_variant.price }}</h5>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
  
<script>
export default {
  props: {
    products: Array,
    selectedFilters: Array,
  },
  methods: {
    // Method to remove a selected filter
    removeFilter(index) {
      this.$emit("removeFilter", index);
    },
    toggleFilters() {
      this.$emit("toggleFilters");
    },
  },
};
</script>
<style scoped>
.shop__selected-filters {
  margin-bottom: 15px;
}

.selected-filter {
  display: inline-block;
  margin-right: 10px;
  padding: 5px 10px;
  background-color: #f0f0f0;
  border: 1px solid #ccc;
  border-radius: 3px;
  font-size: 14px;
  position: relative;
}

.close-icon {
  cursor: pointer;
  position: relative;
  top: 0;
  right: 0;
  color: #999;
  font-size: 12px;
}
</style>