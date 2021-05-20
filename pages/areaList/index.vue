<template>

    <div class="c1">
      <div class="c2">
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item>
            <nuxt-link :to="{name: 'countryList', params:{state: true}}">
              Country List
            </nuxt-link>
          </el-breadcrumb-item>
          <el-breadcrumb-item>Area List</el-breadcrumb-item>
        </el-breadcrumb>
      </div>

      <h1 class="c3">cities in {{this.currentCountry}}</h1>
      <el-col :span="4" v-for="area in areaList" :key="area.item">
        <nuxt-link :to="{name: 'areaMessage', params:{area: area, country: currentCountry, code: code,
        locate: locate, currency: currency}}">
          {{area}}
        </nuxt-link>
      </el-col>
    </div>
</template>

<script>
    import axios from "axios";

    export default {
        components: {},
        data() {
            return {
              currentCountry:'',
              areaList:[],
              date:'',
              code:'',
              locate:'',
              currency: ''
            }
        },
        computed: {},
        // 监控data中的数据变化
        watch: {},

        // 方法集合
        methods: {
          getAreaList(){
            this.currentCountry = this.$route.params.name
            this.code = this.$route.params.code
            this.locate = this.$route.params.locate
            this.currency =  this.$route.params.currency
            // console.log('locate: ',this.currency)
            // console.log('ccoun: ', this.currentCountry)
            const options = {
              method: 'GET',
              url: 'https://countriesnow.space/api/v0.1/countries',
              headers: { }
            };
            axios.request(options).then(response=> {
              // console.log('aaa: ',response.data.data)
              for(var i = 0; i< response.data.data.length; i++) {
                if (response.data.data[i].country == this.currentCountry){
                    // console.log('i is: ', i)
                    this.areaList = response.data.data[i].cities
                }
                if(this.currentCountry == 'United States of America'){
                  if (response.data.data[i].country == 'United States'){
                    // console.log('i is: ', i)
                    this.areaList = response.data.data[i].cities
                  }
                }
              }
              this.$forceUpdate()
            }).catch(function (error) {
              console.error(error);
            });
          }
        },

        created() {
          this.getAreaList()
        },
    }
</script>

<style scoped>

  .c1{
    margin-top: 30px;
    margin-left: 15px;
  }
  .c2{
    margin-top: 10px;
    margin-bottom: 20px;
  }
  .c3{
    margin-bottom: 25px;
  }
</style>

