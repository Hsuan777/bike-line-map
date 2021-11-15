<template>
  <div>
    <figure class="banner banner__index magic-height-400 mb-15">
      <div class="container h-100">
        <div class="row align-items-center h-100">
          <div class="col-md-6 mx-auto">
            <h2 class="text-center text-white mb-5">
              尋找附近 U-Bike
            </h2>
            <div class="p-5 bg-white rounded">
              <div class="d-flex align-items-center">
                <button
                  type="button"
                  class="btn btn-lg btn-info me-5 d-flex align-items-center"
                  @click="getUserPosition"
                >
                  <span class="material-icons-outlined text-white me-3"> person_pin_circle </span>
                  以我所在的位置搜尋
                </button>
                <p class="text-danger d-flex align-items-center">
                  <span class="material-icons-outlined me-3"> warning </span>
                  此功能需開啟衛星定位
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </figure>
    <div class="container mb-15">
      <div class="row">
        <div class="col-md-2">
          <ul class="list-group list-group-flush">
            <li
              class="
                list-group-item list-group-item-action
                d-flex
                justify-content-center
                align-items-center
              "
            >
              <span class="material-icons-outlined text-info me-3"> person_pin_circle </span>
              <p>目前位置</p>
            </li>
            <li
              class="
                list-group-item list-group-item-action
                d-flex
                justify-content-center
                align-items-center
              "
            >
              <span class="material-icons-outlined text-success me-3"> location_on </span>
              <p>正常租借</p>
            </li>
            <li
              class="
                list-group-item list-group-item-action
                d-flex
                justify-content-center
                align-items-center
              "
            >
              <span class="material-icons-outlined text-primary me-3"> location_on </span>
              <p>無車可借</p>
            </li>
            <li
              class="
                list-group-item list-group-item-action
                d-flex
                justify-content-center
                align-items-center
              "
            >
              <span class="material-icons-outlined text-danger me-3"> location_on </span>
              <p>車位已滿</p>
            </li>
            <li
              class="
                list-group-item list-group-item-action
                d-flex
                justify-content-center
                align-items-center
              "
            >
              <span class="material-icons-outlined text-dark me-3"> location_on </span>
              <p>暫停營運</p>
            </li>
          </ul>
        </div>
        <div class="col-md-10">
          <div id="mapid" class="magic-height-400" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      locationUser: {},
      leaflet: {},
      bikeData: [],
    };
  },
  created() {
    if (process.client) {
      this.leaflet = require('leaflet');
    }
  },
  mounted() {
    this.getUserPosition();
    this.getBikeData();
  },
  methods: {
    getUserPosition() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(this.success, this.error);
      }
    },
    success(position) {
      this.locationUser = {
        latitude: position.coords.latitude,
        longitude: position.coords.longitude,
      };
      this.myMap = this.leaflet.map('mapid', {
        center: [this.locationUser.latitude, this.locationUser.longitude],
        zoom: 13,
      });
      this.leaflet
        .tileLayer(
          'https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token={accessToken}',
          {
            attribution:
              'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
            maxZoom: 18,
            id: 'mapbox/streets-v11',
            tileSize: 512,
            zoomOffset: -1,
            accessToken:
              'pk.eyJ1IjoidmljMzMzIiwiYSI6ImNrdXRqNHBjbTVwa3Myb21uc3hrajJkeHEifQ.TeelvbEBo9SIQjYpUdv70g',
          },
        )
        .addTo(this.myMap);
      this.leaflet
        .circle([this.locationUser.latitude, this.locationUser.longitude], { radius: 3000 })
        .addTo(this.myMap);
    },
    error() {
      alert('無法取得你的位置');
    },
    getBikeData() {
      const apiUrl = 'https://ptx.transportdata.tw/MOTC/v2/Bike/Station/Taoyuan?$format=JSON';
      this.$axios.get(apiUrl).then((res) => {
        this.bikeData = res.data;
        this.renderBikeData();
      });
    },
    renderBikeData() {},
  },
};
</script>
