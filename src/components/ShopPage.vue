<template>
  <div>
    <div id="preloader" v-if="loading">
      <div class="loader"></div>
    </div>
    <div class="container">
      <HeaderNav />
    </div>
    <div class="row">
      <HeaderMenu :headerMenu="headerMenu" />
    </div>
    <div class="canvas__open" @click="toggleFilterSection">
      <i class="fa fa-bars">
      </i>
    </div>
    <section>
      <div class="container">
        <div class="row">
          <div class="col-lg-12 ml-3">
            <div class="breadcrumb__text">
              <div class="breadcrumb__links p-3">
                <a href="./index.html">Home</a>
                <a href="./index.html"> Beauty </a>
                <span>For Her</span>
              </div>
              <h4 class="d-none d-sm-block">For Her</h4>
            </div>
          </div>
        </div>
      </div>
    </section>
    <section class="shop spad">
      <div class="container">
        <div class="row">
          <div class="col-lg-3">
            <transition name="filter-slide">
              <FilterSection v-if="showFilter" :categories="categories" :brands="brands" @filter="applyFilter"
                @search="performSearch" @updateFilters="updateFilters" @closeFilterSection="closeFilterSection" />
            </transition>
          </div>
          <div class="col-lg-9" v-if="showProductList">
            <ProductList :products="filteredProducts" :selectedFilters="selectedFilters" @removeFilter="removeFilter"
              @toggleFilters="toggleFilters" />
          </div>
        </div>
      </div>
    </section>
    <div class="row">
      <div class="col-lg-12">
        <div class="product__pagination">
          <a class="active" href="#">1</a>
          <a href="#">2</a>
          <a href="#">3</a>
          <span>...</span>
          <a href="#">21</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import ProductList from "@/components/ProductList";
import FilterSection from "@/components/FilterSection";
import HeaderNav from "@/components/HeaderNav.vue";
import HeaderMenu from "@/components/HeaderMenu.vue";

export default {
  components: {
    ProductList,
    FilterSection,
    HeaderNav,
    HeaderMenu,
  },
  data() {
    return {
      loading: false,
      showFilter: false,
      showProductList: true,
      products: [],
      categories: [],
      brands: [],
      selectedCategory: null,
      selectedBrand: null,
      selectedFilters: [],
      headerMenu: [
        { text: 'Grocery', link: '#' },
        { text: 'Beauty', link: '#', active: true },
        { text: 'Electronics', link: '#' },
        { text: 'Blog', link: '#' },
        { text: 'Clothing', link: '#' },
        { text: 'Furniture', link: '#' },
        { text: 'Toys & Video games ', link: '#' },
        { text: 'Baby', link: '#' },
        { text: 'Personal Care', link: '#' },
        { text: 'Pets', link: '#' },
        { text: 'Sports', link: '#' },
        { text: 'Stationery', link: '#' },
      ],
    };
  },
  computed: {
    filteredProducts() {
      return this.products;
    },

  },
  mounted() {
    this.fetchData();
    window.addEventListener("resize", this.handleResize);
    this.handleResize();
  },
  beforeDestroy() {
    window.removeEventListener("resize", this.handleResize);
  },
  methods: {
    toggleFilterSection() {
      // Toggle the value of showFilter
      this.showFilter = !this.showFilter;
      this.showProductList = !this.showFilter;
    },
    toggleFilters() {
      // Clear the selected filters
      this.selectedCategory = null;
      this.selectedBrand = null;
      this.selectedFilters = [];

      // Make a request to get all products
      this.fetchFilteredData();
    },

    handleResize() {
    
      this.showFilter = window.innerWidth >= 768;
    },
    closeFilterSection(value) {
      this.showFilter = value;
      this.showProductList = !this.showFilter;
    },
    applyFilter(selectedCategories, selectedBrands) {
      this.selectedCategory = selectedCategories;
      this.selectedBrand = selectedBrands;

      this.fetchFilteredData();
    },
    async fetchFilteredData() {
      try {
        this.loading = true;
        const filterParams = {};

        if (this.selectedCategory && this.selectedCategory.length > 0) {
          filterParams.categories = this.selectedCategory.join(",");
        }

        if (this.selectedBrand && this.selectedBrand.length > 0) {
          filterParams.brands = this.selectedBrand.join(",");
        }

        const response = await axios.get(
          "https://joulia.dashboard.hamburgermenu.app/api/v1/products",
          {
            params: { filter: filterParams },
          }
        );

        this.products = response.data.data;
      } catch (error) {
        console.error("Error fetching filtered data", error);
      } finally {
        this.loading = false;
      }
    },
    async fetchData() {
      try {
        this.loading = true;
        const [productsResponse, categoriesResponse, brandsResponse] = await Promise.all([
          axios.get("https://joulia.dashboard.hamburgermenu.app/api/v1/products"),
          axios.get("https://joulia.dashboard.hamburgermenu.app/api/v1/categories"),
          axios.get("https://joulia.dashboard.hamburgermenu.app/api/v1/brands"),
        ]);

        this.products = productsResponse.data.data;
        this.categories = categoriesResponse.data.data;
        this.brands = brandsResponse.data.data;
        console.log(this.categories);
      } catch (error) {
        console.error("Error fetching data", error);
      }
      finally {
        this.loading = false; 
      }
    },
    removeFilter(index) {
     
      const removedFilter = this.selectedFilters.splice(index, 1)[0];

      // Update selectedCategory and selectedBrand based on the remaining filters
      this.selectedCategory = this.selectedFilters
        .filter(filter => filter.startsWith("Category"))
        .map(filter => filter.replace("Category: ", ""));
      this.selectedBrand = this.selectedFilters
        .filter(filter => filter.startsWith("Brand"))
        .map(filter => filter.replace("Brand: ", ""));

      // Update selectedFilters to include only the remaining filters
      this.selectedFilters = this.selectedFilters.filter(filter => filter !== removedFilter);

      // Fetch data with the updated filters
      this.fetchFilteredData();
    },

    updateFilters(selectedCategories, selectedBrands) {
      // Clear the existing filters
      this.selectedFilters = [];

      // Add selected categories to the filters array
      selectedCategories.forEach(categoryId => {
        const category = this.categories.find(c => c.id === categoryId);
        if (category) {
          this.selectedFilters.push(`Category: ${category.title}`);
        }
      });

      // Add selected brands to the filters array
      selectedBrands.forEach(brandId => {
        const brand = this.brands.find(b => b.id === brandId);
        if (brand) {
          this.selectedFilters.push(`Brand: ${brand.title}`);
        }
      });
    },
  },
};
</script>

<style scoped>

.filter-slide-enter-active,
.filter-slide-leave-active {
  transition: max-height 1s, opacity 1s;
}

.filter-slide-enter,
.filter-slide-leave-to

  {
  max-height: 0;
  overflow: hidden;
  opacity: 0;
}
</style>