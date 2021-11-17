<template>
  <div>
    <figure class="banner banner__index magic-height-400 mb-8 mb-lg-15">
      <div class="container h-100">
        <div class="row align-items-center h-100">
          <div class="col-md-6 mx-auto">
            <h2 class="text-center text-white mb-5">
              尋找附近 U-Bike
            </h2>
            <div class="p-5 bg-white rounded">
              <div class="row g-2 mb-5">
                <div class="col-lg-6">
                  <select
                    class="form-select py-3 border-secondary"
                    aria-label="Default select example"
                    @change="changeCity($event.target.value)"
                  >
                    <option
                      :selected="nowCityName === '請選擇城市'"
                      :hidden="nowCityName !== '請選擇城市'"
                    >
                      請選擇城市
                    </option>
                    <option
                      v-for="(item, index) in cities"
                      :key="item.name + index"
                      :value="item.name"
                      :selected="item.name === nowCityName"
                    >
                      {{ item.name }}
                    </option>
                  </select>
                </div>
                <div class="col-lg-6 mb-5">
                  <input
                    v-model="cacheSearch"
                    class="form-control py-3 border-secondary"
                    placeholder="請輸入站點名稱"
                    :disabled="nowCityName === '請選擇城市'"
                    @change="renderBikeData"
                  >
                </div>
                <div class="col-lg-6">
                  <button
                    type="button"
                    class="btn btn-lg btn-info me-5 d-flex align-items-center w-100"
                    @click="setLocation"
                  >
                    <span class="material-icons-outlined text-white me-3"> person_pin_circle </span>
                    以我所在的位置搜尋
                  </button>
                </div>
                <div class="col-lg-6 d-flex align-items-center">
                  <p class="text-danger d-flex align-items-center">
                    <span class="material-icons-outlined me-3"> warning </span>
                    此功能需開啟衛星定位
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </figure>
    <div class="container mb-15">
      <div class="row">
        <div class="col-md-2 order-2 order-md-1">
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
          <input
            v-model="cacheSearch"
            class="form-control py-3 mb-5 border-secondary text-center d-none d-lg-block"
            placeholder="請輸入站點名稱"
            :disabled="nowCityName === '請選擇城市'"
            @change="renderBikeData"
          >
          <div v-if="cacheSearch" class="d-none d-lg-block">
            <p class="mb-3">
              站點列表
            </p>
            <ul class="list-group list-group-flush overflow-auto magic-height-300 mb-5">
              <li
                v-for="item in filterBikeData"
                :key="item.StationUID"
                class="list-group-item list-group-item-action"
                @click="
                  setStation(item.StationPosition.PositionLat, item.StationPosition.PositionLon)
                "
              >
                {{ item.StationName.Zh_tw }}
              </li>
            </ul>
          </div>
        </div>
        <div class="col-md-10 order-1 order-md-2">
          <div v-if="cacheSearch" class="d-lg-none">
            <p class="mb-3">
              站點列表
            </p>
            <ul class="list-group list-group-flush overflow-auto magic-height-300 mb-5">
              <li
                v-for="item in filterBikeData"
                :key="item.StationUID"
                class="list-group-item list-group-item-action"
                @click="
                  setStation(item.StationPosition.PositionLat, item.StationPosition.PositionLon)
                "
              >
                {{ item.StationName.Zh_tw }}
              </li>
            </ul>
          </div>
          <div id="mapID" ref="mapID" class="magic-height-600" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import JsSHA from 'jssha';

export default {
  data() {
    return {
      locationUser: {},
      leaflet: {},
      bikeData: [],
      availabilityBikeData: [],
      nowCityName: '請選擇城市',
      cacheSearch: '',
      cities: [
        {
          name: '臺北市',
          nameEn: 'Taipei',
          coordinate: {
            latitude: 25.0462,
            longitude: 121.5174,
          },
        },
        {
          name: '新北市',
          nameEn: 'NewTaipei',
          coordinate: {
            latitude: 25.0143926,
            longitude: 121.4639425,
          },
        },
        {
          name: '桃園市',
          nameEn: 'Taoyuan',
          coordinate: {
            latitude: 24.989306,
            longitude: 121.3133955,
          },
        },
        {
          name: '臺中市',
          nameEn: 'Taichung',
          coordinate: {
            latitude: 24.136941,
            longitude: 120.685056,
          },
        },
        {
          name: '臺南市',
          nameEn: 'Tainan',
          coordinate: {
            latitude: 22.997212,
            longitude: 120.212319,
          },
        },
        {
          name: '高雄市',
          nameEn: 'Kaohsiung',
          coordinate: {
            latitude: 22.6384542,
            longitude: 120.3019452,
          },
        },
        {
          name: '新竹市',
          nameEn: 'Hsinchu',
          coordinate: {
            latitude: 24.801841,
            longitude: 120.9715962,
          },
        },
        {
          name: '苗栗縣',
          nameEn: 'MiaoliCounty',
          coordinate: {
            latitude: 24.5699868,
            longitude: 120.8223152,
          },
        },
        {
          name: '嘉義市',
          nameEn: 'Chiayi',
          coordinate: {
            latitude: 23.4791004,
            longitude: 120.4413128,
          },
        },
        {
          name: '屏東縣',
          nameEn: 'PingtungCounty',
          coordinate: {
            latitude: 22.669248,
            longitude: 120.4861926,
          },
        },
        {
          name: '金門縣',
          nameEn: 'KinmenCounty',
          coordinate: {
            latitude: 24.409293,
            longitude: 118.297844,
          },
        },
      ],
    };
  },
  computed: {
    filterBikeData() {
      return this.bikeData.filter((item) => item.StationName.Zh_tw.match(this.cacheSearch));
    },
  },
  created() {
    if (process.client) {
      this.leaflet = require('leaflet');
      const markerCluster = require('leaflet.markercluster');
      this.leaflet = { ...this.leaflet, ...markerCluster };
    }
  },
  mounted() {
    this.getUserPosition();
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
      this.myMap = this.leaflet.map('mapID', {
        center: [position.coords.latitude, position.coords.longitude],
        zoom: 14,
      });
      // 加入圖層
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
      // 顯示使用者座標方圓區域
      this.leaflet
        .circle([this.locationUser.latitude, this.locationUser.longitude], { radius: 3000 })
        .addTo(this.myMap);
      // 標示使用者座標
      this.leaflet
        .marker([this.locationUser.latitude, this.locationUser.longitude], {
          icon: this.setIconColor('blue'),
        })
        .addTo(this.myMap);
    },
    error() {
      alert('無法取得你的位置');
    },
    getBikeData(city) {
      const apiUrl = `https://ptx.transportdata.tw/MOTC/v2/Bike/Station/${city}?$format=JSON`;
      const availabilityApiUrl = `https://ptx.transportdata.tw/MOTC/v2/Bike/Availability/${city}?$format=JSON`;
      this.$axios
        .get(apiUrl, {
          headers: this.getAuthorizationHeader(),
        })
        .then((res) => {
          this.bikeData = res.data;
          this.$axios
            .get(availabilityApiUrl, {
              headers: this.getAuthorizationHeader(),
            })
            .then((respons) => {
              this.availabilityBikeData = respons.data;
              this.bikeData.forEach((item, index) => {
                this.availabilityBikeData.forEach((availabilityItem) => {
                  if (item.StationUID === availabilityItem.StationUID) {
                    this.bikeData[index] = { ...item, ...availabilityItem };
                  }
                });
              });
              this.renderBikeData();
            });
        });
    },
    setIconColor(color) {
      const iconColor = new this.leaflet.Icon({
        iconUrl: `https://cdn.rawgit.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-${color}.png`,
        iconSize: [25, 41],
        iconAnchor: [12, 41],
        popupAnchor: [1, -34],
        shadowSize: [41, 41],
      });
      return iconColor;
    },
    renderBikeData() {
      const markers = this.leaflet.markerClusterGroup();
      this.bikeData.forEach((item) => {
        let iconColor = '';
        if (item.ServiceStatus === 1 && item.AvailableRentBikes > 0) {
          iconColor = 'green';
        } else if (item.ServiceStatus === 1 && item.AvailableRentBikes === 0) {
          iconColor = 'yellow';
        } else if (item.ServiceStatus === 1 && item.AvailableReturnBikes === 0) {
          iconColor = 'red';
        } else {
          iconColor = 'black';
        }
        // 若單獨使用 marker，可以接續 addTo() 將其加入地圖，但使用群組就不需要
        markers.addLayer(
          this.leaflet.marker(
            [item.StationPosition.PositionLat, item.StationPosition.PositionLon],
            {
              icon: this.setIconColor(iconColor),
            },
          ).bindPopup(`<p>${item.StationName.Zh_tw}</p>
            <p>營運狀態: ${item.ServiceStatus === 1 ? '正常營運' : '暫停營運'}</p>
            <p>可借數量: ${item.AvailableRentBikes}</p>
            <p>可歸還量: ${item.AvailableReturnBikes}</p>`),
        );
      });
      this.myMap.addLayer(markers);
    },
    getAuthorizationHeader() {
      const AppID = '3209d3c409014e8cb42b5e83f861c102';
      const AppKey = 'qd4_Nh2b30-edGu3vXmbDzaWTFU';
      const GMTString = new Date().toGMTString();
      const ShaObj = new JsSHA('SHA-1', 'TEXT');
      ShaObj.setHMACKey(AppKey, 'TEXT');
      ShaObj.update(`x-date: ${GMTString}`);
      const HMAC = ShaObj.getHMAC('B64');
      const Authorization = `hmac username="${AppID}", algorithm="hmac-sha1", headers="x-date", signature="${HMAC}"`;
      return { Authorization, 'X-Date': GMTString };
    },
    // 以自身為中心點
    setLocation() {
      this.nowCityName = '請選擇城市';
      this.cacheSearch = '';
      this.myMap.setView([this.locationUser.latitude, this.locationUser.longitude], 13);
    },
    // 以站點為中心點
    setStation(latitude, longitude) {
      this.myMap.setView([latitude, longitude], 18);
      this.leaflet.marker([latitude, longitude]).openPopup();
    },
    // 以城市車站為中心點
    changeCity(value) {
      this.nowCityName = value;
      this.cacheSearch = '';
      const newCity = this.cities.filter((item) => item.name === value)[0];
      this.myMap.setView([newCity.coordinate.latitude, newCity.coordinate.longitude], 13);
      this.getBikeData(newCity.nameEn);
    },
  },
};
</script>
