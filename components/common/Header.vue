<template>
  <header class="header">

    <div class="header__logo">
      Funiro.
    </div>
    <div class="header__items">
      <div class="header__items--item" v-for="(item, i) in headerItems" :key="i">
        {{ item.text }}
      </div>
      <BaseIcon class="ic-size-20">dropdownArrow</BaseIcon>
    </div>

  </header>
</template>

<script>
export default {
  name: "Header",
  data() {
    return {
      headerItems: [
        {
          text: 'Products',
          id: 1,
          show: false,
        },
        {
          text: 'Rooms',
          id: 2,
          show: false
        },
        {
          text: 'Inspirations',
          id: 3
        },
      ]
    }
  }
}
</script>

<style lang="scss" scoped>

.header{
  display: flex;
  align-items: center;

  &__logo{

  }
}
</style>
