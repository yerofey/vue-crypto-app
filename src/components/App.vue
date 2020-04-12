<template>
  <div id="app">
    <div v-if="isLoading === true">
      <div class="loader">Loading Data...</div>
    </div>
    <div v-else class="app">
      <header class="_bg-light-gray _c-dark-gray _f5 _mb-1 _p2 _ta-c">
        Market Cap: {{ displayShortNumber(global.market_cap) }}
        &nbsp;&middot;
        24h Vol: {{ displayShortNumber(global.volume_24h) }}
      </header>
      <main class="container _center">
        <div class="table">
          <div class="header _bb _b-light-gray">
            <div class="row _c-dark-gray _f5">
            <div class="cell _pt1 _pr1 _pb1 _ta-c">#</div>
            <div class="cell _p1">Name</div>
            <div class="cell _pt1 _pr1 _pb1 _ta-r">Price</div>
            <div class="cell _pt1 _pb1 _tc">24h</div>
            </div>
          </div>
          <div class="content">
            <div class="cryptocurrencies-list">
              <div class="row link-item _bb _b-light-gray _mb1" v-for="cryptocurrency in cryptocurrencies" v-bind:key="cryptocurrency.id">
                <div class="cell _c-gray _f5 _pt1 _pr1 _pb1 _tc">{{ cryptocurrency.rank }}</div>
                <div class="cell _p1">
                  <div class="_c-black _f4">{{ cryptocurrency.name }}</div>
                  <div class="_c-dark-gray _f5 _mt1 _mb1">{{ cryptocurrency.ticker }}</div>
                </div>
                <div class="cell _c-black _f5 _pt1 _pr1 _pb1 _ta-r">
                  {{ displayCoinPrice(cryptocurrency.price) }}
                </div>
                <div class="cell _f5 _pt1 _pb1 _tc" v-bind:class="{ 
                  '_c-red': (cryptocurrency.change_1d.toString().indexOf('-') > -1) ? true : false, 
                  '_c-green': (cryptocurrency.change_1d.toString().indexOf('-') > -1) ? false : true
                  }">
                  {{ parseFloat(cryptocurrency.change_1d).toFixed(2) }}%
                </div>
              </div>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'

  export default {
    name: 'app',
    data() {
      return {
        cryptocurrencies: null,
        global: null,
        fiat: null,
        isLoading: true
      }
    },
    created: function() {
      // get global data
      axios.get('https://api.cryptorian.ru/v1/global')
        .then(response => {
          this.global = response.data.data.row;
        })
        .catch(error => {
          console.error(error);
        });

      // get currencies data
      axios.get('https://api.cryptorian.ru/v1/cryptocurrencies')
        .then(response => {
          this.cryptocurrencies = response.data.data.rows;
          this.fiat = response.data.data.fiat;
          this.isLoading = false;
        })
        .catch(error => {
          console.error(error);
        });
    },
    methods: {
      displayCoinPrice: function(value) {
        return new Intl.NumberFormat('en-EN', { style: 'currency', currency: this.fiat.ticker }).format(value);
      },
      displayShortNumber: function(value, decimals = 1) {
        let format,
          suffix,
          suffixes = [
            'K',
            'M',
            'B',
            'T',
          ];

        if (value < 9e2) {
          format = parseFloat(value).toFixed(decimals);
          suffix = '';
        } else if (value < 9e5) {
          format = parseFloat(value / 1e3).toFixed(decimals);
          suffix = suffixes[0];
        } else if (value < 9e8) {
          format = parseFloat(value / 1e6).toFixed(decimals);
          suffix = suffixes[1];
        } else if (value < 9e11) {
          format = parseFloat(value / 1e9).toFixed(decimals);
          suffix = suffixes[2];
        } else {
          format = parseFloat(value / 1e12).toFixed(decimals);
          suffix = suffixes[3];
        }

        if (decimals > 0) {
          // TODO
        }

        return format + suffix;
      }
    }
  }
</script>

<!-- CSS libraries -->
<style src="normalize.css/normalize.css"></style>

<!-- Global CSS -->
<style>
  #app {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
    "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
    sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }
</style>

<!-- Scoped component css -->
<!-- It only affect current component -->
<style scoped>
  @import '../assets/jamui.min.css';

  .app {
    margin-bottom: 10px;
    
    box-sizing: border-box;
  }

  main {
    width: 330px;
  }

  .loader {
    margin: 50px auto 0;

    width: 200px;

    font-size: 20px;
    text-align: center;
  }

  .table {
    border-collapse: collapse;
  }

  .table .cell {
    display: table-cell;
  }

  .table a.row {
    text-decoration: none !important;
  }

  .table .row {
    display: table-row;
  }

  .table > .content {
    display: table-row-group;
  }

  .table > .header {
    display: table;
    width: 100%;
  }

  .table > .header > div {
    display: table-row;
  }
</style>
