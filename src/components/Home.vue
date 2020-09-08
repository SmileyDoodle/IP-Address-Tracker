<template>
  <div class="main-wrap">
      <div id="header">
        <h1 class="has-text-weight-semibold">IP Address Tracker</h1>
        <div id="search">
            <div class="search-wrap">
                <input
                    class="input is-small is-rounded"  
                    type="text" 
                    placeholder="IP Address or domain..." 
                    v-model="query"
                    @keyup.enter="getIP()" 
                    ref="searchBar"
                />
                <button class="button is-small is-rounded" @click="getIP()">
                    <img :src="require(`../assets/images/icon-arrow.svg`)" alt="enter">
                </button>
            </div>
            <div class="info-wrap">
                <div class="detail-box">
                    <h6 class="has-text-weight-medium">IP ADDRESS</h6>
                    <p class="has-text-weight-light"> {{this.json.query}} </p>
                </div>
                <div class="detail-box">
                    <h6 class="has-text-weight-medium">LOCATION</h6>
                    <p class="has-text-weight-light"> {{this.json.country}} </p>
                </div>
                <div class="detail-box">
                    <h6 class="has-text-weight-medium">TIMEZONE</h6>
                    <p class="has-text-weight-light"> {{this.json.timezone}} </p>
                </div>
                <div class="detail-box">
                    <h6 class="has-text-weight-medium">ISP</h6>
                    <p class="has-text-weight-light"> {{this.json.isp}} </p>
                </div>
            </div>
            <div class="error-wrap" v-if="error"> 
              <h1> Couldn't find this IP. Try again. </h1>
            </div>
        </div>
      </div>
      <div id="map">

      </div>
  </div>
</template>

<script>
import mapboxgl from 'mapbox-gl'

export default {
    name: 'Home',
    data() {
        return {
            query: "",
            json: "",
            error: ""
        }
    },
    methods: {
        getIP() {
            this.error = '';

            fetch(`http://ip-api.com/json/${this.query}`)
                .then(res => {
                    return res.json();
                }).then(result => {
                    this.json = result;
                    this.getMap();
                    this.query = "";
                    this.$refs.searchBar.blur();
                }).catch((err) => {
                    console.log(err);
                    // this.isLoading = false;
                    this.query = "";
                    this.error = err;
                })
        },
        getMap() {
            mapboxgl.accessToken = process.env.VUE_APP_MAPBOX;
            var map = new mapboxgl.Map({
            container: 'map',
            style: 'mapbox://styles/mapbox/dark-v10',
            center: [this.json.lon, this.json.lat],
            zoom: 11
            });
            
            new mapboxgl.Marker()
            .setLngLat([this.json.lon, this.json.lat])
            .addTo(map);
    
            map.on('load', function () {
                map.resize();
            });
        }
    },
    mounted() {
        this.getIP();

        this.$refs.searchBar.focus();
    }
}


</script>

<style>
.main-wrap {
    position: absolute;
    width: 100%;
    height: 100%;
}
#header {
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 30%;
    background-color: #454545;
}
#header h1 {
    font-size: 1.5rem;
    color: #fff;
    margin: 2.5rem 0 1rem;
}
#search {
    position: absolute;
    z-index: 1;
    top: 110px;
    height: 200px;
    width: 65%;
    margin: 0 auto;
    border-radius: 15px;
    background-color: #fff;
    box-shadow: 0px 7px 7px 1px rgb(10 10 10 / 72%);


}
.search-wrap {
    display: flex;
    width: 40%;
    margin: 2.5rem auto 1rem;
}
.search-wrap input {
    margin: 0 0.5rem;
}
.search-wrap button {
    margin: 0 0.5rem;
    color: #fff;
    background-color: #61aeca;
}
.detail-box {
    padding: 0 3rem;
    margin: 1.3rem 0 0.5rem;
    border-right: 1px solid #efefef;
}
.detail-box:last-child {
    border-right: none;
}
.detail-box h6 {
    font-size: 0.8rem;
    color: #454545;
}
.button {
    padding: 10px !important;
}
.detail-box p {
    font-size: 1rem;
    margin-top: 0.5rem;
}
.info-wrap {
    display: flex;
    justify-content: center;
}
.error-wrap {
    margin-top: 4rem;
}
#map {
    height: 70%;
}
</style>