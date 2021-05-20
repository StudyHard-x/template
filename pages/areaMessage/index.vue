<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item>
        <nuxt-link :to="{name: 'countryList', params:{state: true}}">Country List</nuxt-link>
      </el-breadcrumb-item>
      <el-breadcrumb-item>
        <nuxt-link :to="{name: 'areaList',  params: {name: currentCountry, day: date, code: code}}">Area List</nuxt-link>
      </el-breadcrumb-item>
      <el-breadcrumb-item>Weather and Rental</el-breadcrumb-item>
    </el-breadcrumb>
<!--    select date-->
    <div class="c2">
      <span class="demonstration">Select date to check weather and rental information</span>
      <el-date-picker
        v-model="value"
        type="date"
        placeholder="Select date">
      </el-date-picker>
      <span><el-button @click="checkByDate(value)">check</el-button></span>
<!--      show the date-->
      <h3 class="c3">Date: {{date}}</h3>
      <h3 class="c3">Location: {{area}}</h3>
<!--      weather info-->
      <h2> {{weatherState}} Weather Condition</h2>
      <el-table :data="weatherList" style="width: 100%" v-if="!errorWeather">
        <el-table-column prop="avgtemp" label="avg temp" width="180" >
        </el-table-column>
        <el-table-column prop="maxtemp" label="max temp" width="280" >
        </el-table-column>
        <el-table-column prop="mintemp" label="min temp" width="280" >
        </el-table-column>
        <el-table-column prop="humidity" label="humidity" width="280" >
        </el-table-column>
        <el-table-column prop="pressure_mb" label="pressure_mb" width="280" >
        </el-table-column>
      </el-table>
<!--      error message-->
      <h4 class="c4" v-if="errorWeather"> No weather information for this day</h4>
<!--      rental info-->
      <h2 class="c3">Rental Information</h2>
      <el-table :data="rentalList" style="width: 100%" v-if="!errorRental">
        <el-table-column prop="name" label="Name" width="420" >
        </el-table-column>
        <el-table-column prop="starRating" label="starRating" width="280" >
        </el-table-column>
        <el-table-column prop="address.streetAddress" label="streetAddress" width="280" >
        </el-table-column>
      </el-table>

      <h4 class="c4" v-if="errorRental"> No rental information for this day</h4>
    </div>
  </div>
</template>

<script>
    import axios from "axios";

    export default {
        components: {},
        data() {
            return {
              currentState:'',
              weatherList:[{
                avgtemp: '',
                maxtemp: '',
                mintemp: '',
                humidity: '',
                pressure_mb: ''
              }],
              code: '',
              currentCountry: '',
              date:'',
              area:'',
              currency:'',
              locate:'',
              destination_id:[],
              value:'',
              weatherState: '',
              rentalList: [{}],
              errorWeather: false,
              errorRental: false,
              id: []
            }
        },
        computed: {},
        // 监控data中的数据变化
        watch: {},

        // 方法集合
        methods: {
          checkByDate(value) {
            this.getForecastWeather(value)
          },
          getCurrentWeather() {
            this.area = this.$route.params.area;
            this.code = this.$route.params.code;
            this.currentCountry = this.$route.params.country;
            this.weatherState = 'Current'
            // console.log('this.params: ', this.area, this.code, this.currentCountry)
            var aData = new Date();
            console.log(aData) //中国标准时间
            this.date =
              aData.getFullYear() + "-" + (aData.getMonth() + 1) + "-" + aData.getDate();
            const options = {
              method: 'GET',
              url: 'https://weatherapi-com.p.rapidapi.com/current.json',
              params: {q: this.area},
              headers: {
                'x-rapidapi-key': '1c8b5bbcf8mshb17f2d53d19506dp18dd79jsnd62a52a5b6ed',
                'x-rapidapi-host': 'weatherapi-com.p.rapidapi.com'
              }
            };
            axios.request(options).then(response=> {
              console.log(response.data)
              this.weatherList[0].humidity = response.data.current.humidity
              this.weatherList[0].avgtemp = response.data.current.temp_f
              this.weatherList[0].pressure_mb = response.data.current.pressure_mb
              console.log(this.weatherList[0])
              this.$forceUpdate()
            }).catch((error)=> {
              console.error(error);
              this.errorWeather = true
            });
            this.getDestinationsId(this.date)
          },
          getForecastWeather(value){
            this.errorWeather = false

            this.weatherState = 'Forecast'
            this.date =
              value.getFullYear() + "-" + (value.getMonth() + 1) + "-" + value.getDate();
            console.log('area: ',this.area)
            const options = {
              method: 'GET',
              url: 'https://weatherapi-com.p.rapidapi.com/forecast.json',
              params: {q: this.area, dt: this.date},
              headers: {
                'x-rapidapi-key': '1c8b5bbcf8mshb17f2d53d19506dp18dd79jsnd62a52a5b6ed',
                'x-rapidapi-host': 'weatherapi-com.p.rapidapi.com'
              }
            };
            axios.request(options).then(response=> {
              console.log(response.data)
              // console.log('lllll: ', response.data.forecast.forecastday[0].day.avgtemp_c);
              this.weatherList[0].avgtemp = response.data.forecast.forecastday[0].day.avgtemp_c
              this.weatherList[0].maxtemp = response.data.forecast.forecastday[0].day.maxtemp_c
              this.weatherList[0].mintemp = response.data.forecast.forecastday[0].day.mintemp_c
              console.log(this.weatherList[0])
              this.$forceUpdate()
            }).catch((error)=> {
              console.error(error);
              this.errorWeather = true
            });
            this.getDestinationsId(this.date)
          },

          getDestinationsId(date){
            this.area = this.$route.params.area;
            this.currency = this.$route.params.currency
            this.locate = this.$route.params.locate
            console.log('this.params: ', this.currency, this.locate)
            const options = {
              method: 'GET',
              url: 'https://hotels-com-provider.p.rapidapi.com/v1/destinations/search',
              params: {locale: this.locate , currency: this.currency, query: this.area},
              headers: {
                'x-rapidapi-key': '1c8b5bbcf8mshb17f2d53d19506dp18dd79jsnd62a52a5b6ed',
                'x-rapidapi-host': 'hotels-com-provider.p.rapidapi.com'
              }
            };
            axios.request(options).then((response)=> {
              this.destination_id = response.data.suggestions[0].entities
              for (var i =0; i<this.destination_id.length;i++){
                this.id[i] = this.destination_id[i].destinationId
              }
              // console.log(this.id)
              if (this.destination_id = null){
                this.errorRental = true
              }
              else {
                this.getRental(this.id, date)
              }
            }).catch((error)=> {
              console.error(error);
              this.errorRental = true
            });
          },

          getRental(id, date){
            // console.log('locate: ', locate)
            const a = id[0]
            const options = {
              method: 'GET',
              url: 'https://hotels-com-provider.p.rapidapi.com/v1/hotels/search',
              params: {
                destination_id: a,
                checkout_date: date,
                adults_number: '1',
                locale: 'en_US',
                checkin_date: date,
                sort_order: 'STAR_RATING_HIGHEST_FIRST',
                currency: 'USD'
              },
              headers: {
                'x-rapidapi-key': '1c8b5bbcf8mshb17f2d53d19506dp18dd79jsnd62a52a5b6ed',
                'x-rapidapi-host': 'hotels-com-provider.p.rapidapi.com'
              }
            };
            axios.request(options).then((response)=> {
              console.log('rental: ', response.data.searchResults.results);
              this.rentalList = response.data.searchResults.results
              console.log(this.rentalList)
            }).catch((error)=> {
              this.errorRental = true
              console.error(error);
            });
          }
        },
        created() {
          this.getCurrentWeather()
          // this.getDestinationsId()
        },
    }
</script>

<style scoped>
  .c2{
    margin-left: 40px;
    margin-top: 20px;
  }
  .c3{
    margin-top: 30px;
    margin-bottom: 5px;
  }
  .c4{
    margin-top: 4px;
    color: #F56C6C;
  }
</style>

