<template>
  <div>
    <el-row :gutter="20" v-if="!errorMessage">
      <el-col :span="8" v-for="country in countryList" :key="country.item">
        <div v-if="!state">
          <nuxt-link :to="{name: 'holidayList', params:{code: country.alpha2Code, name: country.name, locate: country.locate}}" >
            {{country.name}}
          </nuxt-link>
        </div>
        <div v-if="state">
          <nuxt-link :to="{name: 'areaList', params:{code: country.alpha2Code, name: country.name,
          locate: country.locate, currency: country.currency}}" >
            {{country.name}}
          </nuxt-link>
        </div>
      </el-col>
    </el-row>
    <p v-if="errorMessage">No cities information of this country</p>
  </div>
</template>

<style>
  .el-row {
    padding-top: 20px;
    padding-left: 30px;
    margin-bottom: 20px;
  }
  .el-col {
    border-radius: 4px;
    margin-left: 30px;
  }
</style>
<script>
  import axios from "axios";

  export default {
    data () {
      return {
        countryList: {
          // name: null,
          // alpha2Code: 0
          locate: '',
          currency: ''
        },
        info: null,
        errorMessage: false,
        state: false
      }
    },
    methods:{
      getCountryList(){
        const a = this.$route.params.state
        // console.log('awa: ',this.$route.params.awa)
        // console.log('sssss: ', this.$route.params.state)
        if(a){
          this.state = this.$route.params.state
        }
        // console.log('state: ', this.state)
        const options = {
          method: 'GET',
          url: 'https://restcountries.eu/rest/v2/all',
        };
        axios.request(options).then(response=> {
          console.log(response.data)
          for(var i = 0; i< response.data.length; i++) {
            this.countryList[i] = response.data[i]
            this.countryList[i].locate = response.data[i].languages[0].iso639_1 + '_' + this.countryList[i].alpha2Code
            this.countryList[i].currency = response.data[i].currencies[0].code
            // console.log(this.countryList[i].currency)
          }
          this.$forceUpdate()
        }).catch(function (error) {
            console.error(error);
            this.errorMessage = true
        });
      }
    },
    created() {
      this.getCountryList()
    }
  }
</script>
