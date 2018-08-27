<template>
  <div id="app">
    <left-nav></left-nav>
    <router-view/>
  </div>
</template>

<script>
  import leftNav from '@/components/component/leftNav'
export default {
  name: 'App',
  components:{
    leftNav,
  }
}
</script>

<style>
#app {

}
</style>
