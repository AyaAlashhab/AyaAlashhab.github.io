<template>
<div class="col-lg-12 col-md-12 text-center breadcrumb-option p-1">
    <nav class="header__menu mobile-menu p-1">
      <ul>
          <li v-for="(item, index) in headerMenu" :key="index" :class="{ 'active': item.active }">
            <a :href="item.link">
              {{ item.text }}
            </a>
            <ul v-if="item.subMenu">
              <li v-for="(subItem, subIndex) in item.subMenu" :key="subIndex">
                <a :href="subItem.link">{{ subItem.text }}</a>
              </li>
            </ul>
          </li>
      </ul>
    </nav>
</div>
</template>

<script>
  export default {
    props: {
     headerMenu: Array,
     
    },
  };
</script>