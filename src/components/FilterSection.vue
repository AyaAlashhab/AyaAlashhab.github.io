<template>
  <div>
    <div class="shop__sidebar">
      <div class="shop__sidebar__search">
        <form @submit.prevent="searchLocal">
          <input v-model="searchQuery" type="text" placeholder="Search...">
          <button type="submit"><span class="icon_search"></span></button>
        </form>
      </div>
      <div class="shop__sidebar__accordion">
        <div class="accordion" id="accordionExample">
          <div class="card">
            <div class="card-heading">
              <a @click="toggleCollapse('collapseOne')">Categories</a>
            </div>
            <transition name="collapse">
              <div v-if="isCollapseOneOpen" class="card-body">
                <div class="shop__sidebar__categories">
                  <ul class="nice-scroll">
                    <li v-for="category in filteredCategories" :key="category.id">
                      <input type="checkbox" v-model="selectedCategories" :value="category.id" />
                      <label class="ml-2">{{ category.title }}</label>
                    </li>
                  </ul>
                </div>
              </div>
            </transition>
          </div>
          <div class="card">
            <div class="card-heading">
              <a @click="toggleCollapse('collapseTwo')">Brand</a>
            </div>
            <transition name="collapse">
              <div v-if="isCollapseTwoOpen" class="card-body">
                <div class="shop__sidebar__categories">
                  <ul class="nice-scroll">
                    <li v-for="brand in filteredBrands" :key="brand.id">
                      <input type="checkbox" v-model="selectedBrands" :value="brand.id"
                        :id="'brandCheckbox_' + brand.id" />
                      <label class="ml-2" :for="'brandCheckbox_' + brand.id">{{ brand.title }}</label>
                    </li>
                  </ul>
                </div>
              </div>
            </transition>
          </div>
        </div>
      </div>
    </div>
    <button class="btn btn-outline-primary mt-4" @click="applyFilter">Apply Filters</button>
  </div>
</template>

<script>
export default {
  props: {
    categories: Array,
    brands: Array,
  },
  data() {
    return {
      selectedCategories: [],
      selectedBrands: [],
      searchQuery: "",
      isCollapseOneOpen: true,
      isCollapseTwoOpen: true,
    };
  },
  computed: {
    filteredCategories() {
      return this.filterItems(this.categories, this.searchQuery);
    },
    filteredBrands() {
      return this.filterItems(this.brands, this.searchQuery);
    },
  },
  methods: {
    filterItems(items, query) {
      return items.filter(item =>
        item.title.toLowerCase().includes(query.toLowerCase())
      );
    },
    applyFilter() {
      this.$emit("filter", this.selectedCategories, this.selectedBrands);
      this.$emit("updateFilters", this.selectedCategories, this.selectedBrands);

      if (window.innerWidth < 896) {
        this.$emit("closeFilterSection", false);
      }
    },
    searchLocal() {
      this.filteredCategories = this.filterItems(this.categories, this.searchQuery);
      this.filteredBrands = this.filterItems(this.brands, this.searchQuery);
    },
    toggleCollapse(collapseId) {
      if (collapseId === 'collapseOne') {
        this.isCollapseOneOpen = !this.isCollapseOneOpen;
      } else if (collapseId === 'collapseTwo') {
        this.isCollapseTwoOpen = !this.isCollapseTwoOpen;
      }
    },
  },
};
</script>

<style scoped>
.collapse-enter-active,
.collapse-leave-active {
  transition: max-height 0.1s ease-in-out;
}

.collapse-enter,
.collapse-leave-to
  {
  max-height: 0;
  overflow: hidden;
}
</style>