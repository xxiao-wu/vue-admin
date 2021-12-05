<template>
  <div>
    <Header />
    <div style="display: flex">
      <SideMenu/>
      <router-view style="flex: 1"/>
    </div>
  </div>
</template>

<script>
import Header from "../components/Header";
import SideMenu from "../components/SideMenu";

export default {
  name: "Layout",
  components:{
    SideMenu,
    Header,

  }
}
</script>

<style scoped>

</style>