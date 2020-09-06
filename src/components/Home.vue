<template>
  <div class="main-wrap">
      <div id="header">
        <h1>IP Address Tracker</h1>
        <div id="search">
            <div class="search-wrap">
                <input 
                    type="text" 
                    placeholder="IP Address..." 
                    v-model="query" 
                />
                <button @click="getIP()">Go</button>
            </div>
            <div class="info-wrap">
                <div class="detail-box">
                    <p>IP Address</p>
                    <p> {{this.json.query}} </p>
                </div>
                <div class="detail-box">
                    <p>Location</p>
                    <p> {{this.json.country}} </p>
                </div>
                <div class="detail-box">
                    <p>Timezone</p>
                    <p> {{this.json.timezone}} </p>
                </div>
                <div class="detail-box">
                    <p>ISP</p>
                    <p> {{this.json.isp}} </p>
                </div>
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
        }
    },
    methods: {
        getIP() {
            fetch(`http://ip-api.com/json/${this.query}`)
                .then(res => {
                    return res.json();
                }).then(result => {
                    this.json = result;
                    this.getMap();
                }
                )
        },
        getMap() {
            mapboxgl.accessToken = process.env.VUE_APP_MAPBOX;
            var map = new mapboxgl.Map({
            container: 'map',
            style: 'mapbox://styles/mapbox/dark-v10',
            center: [this.json.lon, this.json.lat],
            zoom: 10
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
    background-color: #232626;
    background-image: url("https://www.transparenttextures.com/patterns/simple-dashed.png");
}
#header h1 {
    color: #fff;
}
#search {
    height: 150px;
    width: 60%;
    margin: 0 auto;
    border-radius: 15px;
    background-color: #fff;
    opacity: 0.9;
}
.detail-box {
    padding: 0 3rem;
}
.info-wrap {
    display: flex;
    justify-content: center;
}
#map {
    height: 70%;
}
</style>