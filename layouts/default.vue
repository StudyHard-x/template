<template>
  <div>
    <el-menu
      :default-active="activeIndex2"
      class="el-menu-demo"
      mode="horizontal"
      @select="handleSelect"
      background-color="#545c64"
      text-color="#fff"
      active-text-color="#ffd04b">
      <el-menu-item index="1">
        <router-link to="/" tag="li" active-class="current">
          Home Page
        </router-link>
      </el-menu-item>
      <el-menu-item index="2">
        <router-link to="/countryList" tag="li" active-class="current">
          Public holiday
        </router-link>
      </el-menu-item>
      <el-menu-item index="3">
        <nuxt-link :to="{name: 'countryList', params:{state: true}}" tag="li" active-class="current">
          Weather and Rental
        </nuxt-link>
      </el-menu-item>
<!--      <el-menu-item index="3">-->
<!--        <router-link to="/areaList" tag="li" active-class="current">-->
<!--          Area List-->
<!--        </router-link>-->
<!--      </el-menu-item>-->
    </el-menu>
    <nuxt/>
  </div>
</template>



<script>
  export default {
    data() {
      return {
        activeIndex: '1',
        activeIndex2: '1'
      };
    },
    methods: {
      handleSelect(key, keyPath) {
        console.log(key, keyPath);
      }
    }
  }
</script>



<style>
html {
  font-family: "Source Sans Pro", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}

*, *:before, *:after {
  box-sizing: border-box;
  margin: 0;
}

.button--green {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #3b8070;
  color: #3b8070;
  text-decoration: none;
  padding: 10px 30px;
}

.button--green:hover {
  color: #fff;
  background-color: #3b8070;
}

.button--grey {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #35495e;
  color: #35495e;
  text-decoration: none;
  padding: 10px 30px;
  margin-left: 15px;
}

.button--grey:hover {
  color: #fff;
  background-color: #35495e;
}
</style>

