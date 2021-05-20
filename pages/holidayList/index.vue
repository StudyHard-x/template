<template>
    <div class="c1">
      <el-dialog
        title="Tips"
        :visible.sync="dialogVisible"
        width="30%">
        <span>No holiday information for this country</span>
        <span slot="footer" class="dialog-footer">
          <el-button type="primary" @click="dialogVisible = false">Confirm</el-button>
        </span>
      </el-dialog>

      <div v-if="!errorMessage">
        <div class="c2">
          <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/countryList' }">Country List</el-breadcrumb-item>
            <el-breadcrumb-item>Holiday List</el-breadcrumb-item>
          </el-breadcrumb>
        </div>
        <span class="demonstration">Select Year to check public holiday</span>
        <el-date-picker
          v-model="value"
          type="year"
          placeholder="Select year">
        </el-date-picker>
        <span><el-button @click="checkByYear(value)">check</el-button></span>
      </div>

      <el-table v-loading="loading" :data="holidayList" style="width: 100%" v-if="!errorMessage">
        <el-table-column prop="date" label="Date" width="180" >
        </el-table-column>
        <el-table-column prop="name" label="Holiday" width="280" >
        </el-table-column>
        <el-table-column prop="localName" label="Local name of holiday" width="280" >
        </el-table-column>
<!--          <el-table-column label="Check weather and rental information" width="280" >-->
<!--&lt;!&ndash;            <nuxt-link :to="{name: 'areaList', params: {name: this.currentCountry}}">check</nuxt-link>&ndash;&gt;-->
<!--            <template slot-scope="scope">-->
<!--              <el-button type="text" size="small" @click="passHandle(scope.row.date)">check</el-button>-->
<!--            </template>-->
<!--          </el-table-column>-->
      </el-table>

      <p v-if="errorMessage">error message: no holiday information in this year</p>

    </div>
</template>

<script>
  import holiday from "../../api/holiday";
  import axios from "axios";

  export default {
    components: {

    },
    data () {
      return {
        holidayList: [],
        info: null,
        errorMessage: false,
        value: '',
        currentCountryCode: '',
        currentCountry:'',
        locate: '',
        loading: false,
        dialogVisible: false
      }
    },
    created() {
      this.getList()
    },
    methods:{
      // passHandle(time) {
      //   // console.log(time)
      //   this.$router.push({
      //     name: 'areaList',
      //     params: {name: this.currentCountry, day: time, code: this.currentCountryCode, locate: this.locate}
      //   })
      // },
      getList(){
        this.loading = true
        const a = this.$route.params.code
        this.currentCountryCode = a
        this.currentCountry = this.$route.params.name
        this.locate = this.$route.params.locate
        const options = {
          method: 'GET',
          url: `https://public-holiday.p.rapidapi.com/2020/${a}`,
          headers: {
            'x-rapidapi-key': '1c8b5bbcf8mshb17f2d53d19506dp18dd79jsnd62a52a5b6ed',
            'x-rapidapi-host': 'public-holiday.p.rapidapi.com'
          }
        };
        axios.request(options).then(response=> {
          console.log('holiday: ',response.data)
          this.holidayList = response.data
          this.loading = false
        })
          .catch(error=> {
            this.errorMessage = "no holiday information for this country"
            this.dialogVisible = true
            this.$forceUpdate()
            if (error) {
            }
          console.error(error);
        });
      },
      checkByYear(year){
        if (year != ''){
          this.loading = true
          console.log(year)
          var getYear = year.getFullYear()
          const options = {
            method: 'GET',
            url: `https://public-holiday.p.rapidapi.com/${getYear}/${this.currentCountryCode}`,
            headers: {
              'x-rapidapi-key': '1c8b5bbcf8mshb17f2d53d19506dp18dd79jsnd62a52a5b6ed',
              'x-rapidapi-host': 'public-holiday.p.rapidapi.com'
            }
          };
          axios.request(options).then(response=> {
            console.log(response.data);
            this.holidayList = response.data
            this.loading = false
          })
            .catch(error=> {
              this.errorMessage = true
              this.$forceUpdate()
              if (error) {
              }
              console.error(error);
            });
        }
      }
    },
  }
</script>

<style>
  .c1{
    margin-top: 30px;
    margin-left: 15px;
  }
  .demonstration{
    font-size: 18px;
  }
  .c2{
    margin-top: 10px;
    margin-bottom: 20px;
  }
</style>

