<template>
    <div class="row">
    <div class="col-lg-12 col-md-6 col-sm-6 col-xs-12" v-for="pool in filterCoin" :key="pool.id">
        <div class="info-box bg-yellow-gradient">
                <span class="info-box-text">
                    <h3>Our Top Miners for {{ pool.coin.name }}[{{ pool.coin.symbol }}]</h3>
                    <hr>
                    <div class="row" v-for="(value,id) in pool.topMiners" :key="id">
                      <div class="column">
                      <br>{{ value.miner }}
                      </div>
                      <div class="column">
                      <br>{{ formatHashrate(value.hashrate,2,"H/s") }}
                      </div>
                      <div class="column">
                      <br>{{ formatHashrate(value.sharesPerSecond,2,"") }}
                      <hr>
                      </div>
                    </div>
                </span>
            </div>
        </div>
      </div>

</template>
  
  <script>
  import axios from 'axios'
  import {ref,computed} from 'vue'
  import {useRoute} from 'vue-router'
    export default {
      setup(){
          
          const pools = ref([]);
          const route = useRoute();
          const id = ref(route.params.id);
          function getPools() {
              axios
              .get('https://pool.flazzard.com/api/pools')
              .then((response) => {
                  //console.log(response.data.pools)
                  pools.value =response.data.pools
                  console.log(response.data.pools)
              })
              .catch((error) => {
                  console.log(error)
              })
              
          }
          const filterCoin = computed(function() {
                  //show if PPLNS - Button is pressed
                  return pools.value.filter((pool) => pool.id==id.value)
          });
          function formatHashrate(value, decimal, unit) {
        if (value === 0) {
            return "0 " + unit;
        } else {
            var si = [
            { value: 1, symbol: "" },
            { value: 1e3, symbol: "k" },
            { value: 1e6, symbol: "M" },
            { value: 1e9, symbol: "G" },
            { value: 1e12, symbol: "T" },
            { value: 1e15, symbol: "P" },
            { value: 1e18, symbol: "E" },
            { value: 1e21, symbol: "Z" },
            { value: 1e24, symbol: "Y" }
            ];
            for (var i = si.length - 1; i > 0; i--) {
            if (value >= si[i].value) {
                break;
            }
            }
            return ((value / si[i].value).toFixed(decimal).replace(/.0+$|(.[0-9]*[1-9])0+$/, "$1") + " " + si[i].symbol + unit);
            }
        }
          return{
            getPools,
            pools,
            filterCoin,
            id,
            formatHashrate
          }
          
  
      },
      mounted() {
          this.getPools();
      },
    
    }
    </script>
  
  <style>
.column {
  float: left;
  width: 50%;
}

/* Clear floats after the columns */
.row:after {
  content: "";
  display: table;
  clear: both;
}
  </style>