<template>
  <div class="card text-center m-3">
        <h3 class="card-header">Country List</h3>
        <div class="card-body">
        <div class="col-lg-12">
        <div class="row">
          <select class="form-control col-4" v-model="selectedType" id="selection">
            <option  value="name" >name</option>
            <option  value="capital">capital</option>
            <option  value="region">region</option>
            <option  value="subregion">subregion</option>
            <option  value="population">population</option>
            <option  value="demonym">demonym</option>
            <option  value="nativeName">nativeName</option>
        </select>
        <input class="form-control col-8" id="query" type="text" placeholder="Search" aria-label="Search" style="margin-bottom: 1rem;" v-model="query">
        </div>
        <div style="text-align: left; margin-bottom: 1rem">
          found {{len}} <span v-if="len=='1'">country</span><span v-else>countries</span>
        </div>
      <div class="table-responsive">
        <table class="table table-bordered">
          <thead>
            <tr>
              <th>#</th>
              <th>name</th>
              <th>calling codes</th>
              <th>capital</th>
              <th>region</th>
              <th>subregion</th>
              <th>population</th>
              <th>latlng</th>
              <th>demonym</th>
              <th>area</th>
              <th>timezones</th>
              <th>borders</th>
              <th>native name</th>
              <th>currencies</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in pageOfItems" :key="item.name">
              <td>{{index + 1}}</td>
              <td>{{item.name}}</td>
              <td><span v-for="call in item.callingCodes" :key="call">{{call}}</span><br v-if="item.callingCodes.length>1"></td>
              <td>{{item.capital}}</td>
              <td>{{item.region}}</td>
              <td>{{item.subregion}}</td>
              <td>{{item.population}}</td>
              <td>{{item.latlng}}</td>
              <td>{{item.demonym}}</td>
              <td>{{item.area}} km<sup>2</sup></td>
              <td><div v-for="zone in item.timezones" :key="zone">{{zone}}</div></td>
              <td><div v-for="border in item.borders" :key="border">{{border}}</div></td>
              <td>{{item.nativeName}}</td>
              <td>
                <div v-for="currency in item.currencies" :key="currency.code">
                  <div v-if="currency.code">code: <span style="font-weight: 600;">{{currency.code}}</span></div>
                  <div v-if="currency.name">name: <span style="font-weight: 600;">{{currency.name}}</span></div>
                  <div v-if="currency.symbol">symbol: <span style="font-weight: 600;">{{currency.symbol}}</span></div>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
        </div>
        <div class="card-footer pb-0 pt-3">
            <jw-pagination :initialPage=1 :maxPages=3 :pageSize=5 :items="computedCountries" @changePage="onChangePage" :labels="customLabels"></jw-pagination>
        </div>
    </div>
</template>

<script>

const customLabels = {
    first: '<<',
    last: '>>',
    previous: '<',
    next: '>'
};
export default {
  name: "Home",
  data() {
        return {
           customLabels,
           pageOfItems: [],
           countries: null,
           query: "",
           computedCountries: null,
           selectedType: "name",
           len: ""
        };
    },
    created() {
      var self = this
      fetch("https://restcountries.eu/rest/v2/all").then(function(response) {
        if (response.ok) {
          response.json().then(function(json) {
            self.countries = json
            self.computedCountries = json
            self.len = self.computedCountries.length
            console.log(self.countries)
          })
        } else {
          console.log('Network request for products.json failed with response ' + response.status + ': ' + response.statusText)
        }
      })
    },
    mounted() {
      console.log(this.countries)
      var vm = this
      document.getElementById("query").addEventListener("input", function () {
        console.log(vm.selectedType)
        var property = vm.selectedType

          vm.computedCountries = vm.countries.filter(function(item) {
            return (item[property].toLowerCase().indexOf(vm.query.toLowerCase()) !== -1)
          })
        vm.len = vm.computedCountries.length
      })
    },
    
    methods: {
        onChangePage(pageOfItems) {
            // update page of items
            this.pageOfItems = pageOfItems;
        }
    }
};
</script>
