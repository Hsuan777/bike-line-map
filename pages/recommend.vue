<template>
  <div>
    <figure class="banner banner__recommend magic-height-300 mb-8 mb-lg-15">
      <div class="container d-flex align-items-end h-100">
        <h2 class="mb-5 fz-huge fw-bolder zi-9">
          自行車路線
        </h2>
      </div>
    </figure>
    <section class="container bg-secondary py-5 mb-5 rounded">
      <div class="row">
        <div class="col-md-6">
          <select
            class="form-select py-3 border-secondary"
            aria-label="Default select example"
            @change="changeCity($event.target.value)"
          >
            <option :selected="nowCityName === '請選擇城市'" :hidden="nowCityName !== '請選擇城市'">
              請選擇城市
            </option>
            <option
              v-for="(item, index) in cities"
              :key="item.name + index"
              :value="item.nameEn"
              :selected="item.name === nowCityName"
            >
              {{ item.name }}
            </option>
          </select>
        </div>
        <div class="col-md-6">
          <input
            v-model="cacheSearch"
            class="form-control py-3 border-secondary"
            placeholder="請輸入關鍵字"
            :disabled="nowCityName === '請選擇城市'"
          >
        </div>
      </div>
    </section>
    <section class="container mb-8 mb-lg-15">
      <div class="row">
        <div class="col-lg-5 mb-8 mb-lg-0">
          <ul class="list-unstyled justify-content-center overflow-auto magic-height-600">
            <li
              v-for="(item, index) in filterCyclingData"
              :key="item.RouteName + index"
              class="border border-secondary me-2 mb-2"
              @click="changeLayer(item.Geometry)"
            >
              <div class="p-3">
                <h2 class="fz-larger">
                  {{ item.RouteName }}
                </h2>
                <ul class="list-unstyled row">
                  <li class="col-lg-8">
                    <p>開始地點: {{ item.RoadSectionStart }}</p>
                  </li>
                  <li class="col-lg-4">
                    <p v-if="item.Direction">
                      車道類型: {{ item.Direction }}
                    </p>
                    <p v-else>
                      車道類型: 無說明
                    </p>
                  </li>
                  <li class="col-lg-8">
                    <p>結束地點: {{ item.RoadSectionEnd }}</p>
                  </li>
                  <li class="col-lg-4">
                    <p>車道長度: {{ item.CyclingLength }}</p>
                  </li>
                </ul>
              </div>
            </li>
          </ul>
        </div>
        <div class="col-lg-7">
          <div id="mapID" ref="mapID" class="magic-height-600" />
        </div>
      </div>
    </section>
    <section v-if="scenicSpotData[0]" class="container mb-8 mb-lg-15">
      <p class="mb-5 fz-larger">
        附近景點(方圓兩公里)
      </p>
      <swiper :options="swiperOption">
        <swiper-slide v-for="item in scenicSpotData" :key="item.ID" class="">
          <div class="border rounded-4 h-100">
            <a
              class="stretched-link"
              @click.prevent="
                setScenicSpotCoordinate(
                  item.Name,
                  item.Position.PositionLat,
                  item.Position.PositionLon,
                )
              "
            >
              <img
                v-if="item.Picture.PictureUrl1"
                :src="item.Picture.PictureUrl1"
                :alt="item.Picture.PictureDescription1"
                class="img img__card magic-height-128 magic-height-md-190"
              >
              <p
                v-else
                class="
                  img
                  magic-height-128 magic-height-md-190
                  bg-grey
                  text-secondary
                  fz-large
                  d-flex
                  justify-content-center
                  align-items-center
                "
              >
                暫未提供
              </p>
            </a>
            <div class="py-3 px-4">
              <h2 class="fz-medium mb-2">
                {{ item.Name }}
              </h2>
              <p class="text-info mb-3 d-flex">
                {{ item.City }}
              </p>
              <p class="text-truncate">
                {{ item.Description }}
              </p>
            </div>
          </div>
        </swiper-slide>
      </swiper>
    </section>
    <section v-if="restaurantData[0]" class="container mb-8 mb-lg-15">
      <p class="mb-5 fz-larger">
        附近餐飲(方圓兩公里)
      </p>
      <swiper :options="swiperOption">
        <swiper-slide v-for="item in restaurantData" :key="item.ID">
          <div class="border rounded-4 h-100">
            <a
              class="stretched-link"
              @click.prevent="
                setScenicSpotCoordinate(
                  item.Name,
                  item.Position.PositionLat,
                  item.Position.PositionLon,
                )
              "
            >
              <img
                v-if="item.Picture.PictureUrl1"
                :src="item.Picture.PictureUrl1"
                :alt="item.Picture.PictureDescription1"
                class="img img__card magic-height-128 magic-height-md-190"
              >
              <p
                v-else
                class="
                  img
                  magic-height-128 magic-height-md-190
                  bg-grey
                  text-secondary
                  fz-large
                  d-flex
                  justify-content-center
                  align-items-center
                "
              >
                暫未提供
              </p>
            </a>
            <div class="py-3 px-4">
              <h2 class="fz-medium mb-2">
                {{ item.Name }}
              </h2>
              <p class="text-info mb-3 d-flex">
                {{ item.City }}
              </p>
              <p class="text-truncate">
                {{ item.Description }}
              </p>
            </div>
          </div>
        </swiper-slide>
      </swiper>
    </section>
  </div>
</template>
<script>
import JsSHA from 'jssha';
import Wicket from 'wicket';

export default {
  data() {
    return {
      cacheSearch: '',
      nowCityName: '',
      nowType: '',
      cities: [
        {
          name: '臺北市',
          nameEn: 'Taipei',
        },
        {
          name: '新北市',
          nameEn: 'NewTaipei',
        },
        {
          name: '桃園市',
          nameEn: 'Taoyuan',
        },
        {
          name: '臺中市',
          nameEn: 'Taichung',
        },
        {
          name: '臺南市',
          nameEn: 'Tainan',
        },
        {
          name: '高雄市',
          nameEn: 'Kaohsiung',
        },
        {
          name: '基隆市',
          nameEn: 'Keelung',
        },
        {
          name: '新竹市',
          nameEn: 'Hsinchu',
        },
        {
          name: '新竹縣',
          nameEn: 'HsinchuCounty',
        },
        {
          name: '苗栗縣',
          nameEn: 'MiaoliCounty',
        },
        {
          name: '彰化縣',
          nameEn: 'ChanghuaCounty',
        },
        {
          name: '南投縣',
          nameEn: 'NantouCounty',
        },
        {
          name: '雲林縣',
          nameEn: 'YunlinCounty',
        },
        {
          name: '嘉義縣',
          nameEn: 'ChiayiCounty',
        },
        {
          name: '嘉義市',
          nameEn: 'Chiayi',
        },
        {
          name: '屏東縣',
          nameEn: 'PingtungCounty',
        },
        {
          name: '宜蘭縣',
          nameEn: 'YilanCounty',
        },
        {
          name: '花蓮縣',
          nameEn: 'HualienCounty',
        },
        {
          name: '臺東縣',
          nameEn: 'TaitungCounty',
        },
        {
          name: '金門縣',
          nameEn: 'KinmenCounty',
        },
        {
          name: '澎湖縣',
          nameEn: 'PenghuCounty',
        },
        {
          name: '連江縣',
          nameEn: 'LienchiangCounty',
        },
      ],
      cyclingData: [],
      myLayer: null,
      scenicSpotData: [],
      restaurantData: [],
      swiperOption: {
        spaceBetween: 10,
        // loop: true,
        effect: 'fade',
        pagination: {
          el: '.swiper-pagination',
          clickable: true,
        },
        navigation: {
          nextEl: '.swiper-button-next',
          prevEl: '.swiper-button-prev',
        },
        breakpoints: {
          1024: {
            slidesPerView: 4,
            spaceBetween: 10,
          },
          768: {
            slidesPerView: 2,
            spaceBetween: 10,
          },
          640: {
            slidesPerView: 2,
            spaceBetween: 10,
          },
          320: {
            slidesPerView: 1,
            spaceBetween: 10,
          },
        },
      },
    };
  },
  computed: {
    filterCyclingData() {
      return this.cyclingData.filter((item) => item.RouteName.match(this.cacheSearch));
    },
  },
  created() {
    if (process.client) {
      this.leaflet = require('leaflet');
      this.swiper = require('swiper');
    }
  },
  mounted() {
    this.getCyclingData('Taipei');
    this.displayMap();
  },
  methods: {
    getCyclingData(city) {
      const apiUrl = `https://ptx.transportdata.tw/MOTC/v2/Cycling/Shape/${city}?$format=JSON`;
      this.$axios
        .get(apiUrl, {
          headers: this.getAuthorizationHeader(),
        })
        .then((res) => {
          this.cyclingData = res.data;
        });
    },
    getScenicSpotData(latitude, longitude) {
      const apiUrl = `https://ptx.transportdata.tw/MOTC/v2/Tourism/ScenicSpot?$spatialFilter=nearby(${latitude}%2C%20${longitude}%2C%202000)&$format=JSON`;
      this.$axios.get(apiUrl).then((res) => {
        this.scenicSpotData = res.data;
      });
    },
    getRestaurantData(latitude, longitude) {
      const apiUrl = `https://ptx.transportdata.tw/MOTC/v2/Tourism/Restaurant?$spatialFilter=nearby(${latitude}%2C%20${longitude}%2C%202000)&$format=JSON`;
      this.$axios.get(apiUrl).then((res) => {
        this.restaurantData = res.data;
      });
    },
    changeCity(value) {
      this.nowCityName = value;
      this.cacheSearch = '';
      this.getCyclingData(value);
    },
    changeLayer(geo) {
      this.polyLine(geo);
    },
    setScenicSpotCoordinate(name, latitude, longitude) {
      if (this.cacheCoordinate) {
        this.myMap.removeLayer(this.cacheCoordinate);
      }
      // 顯示對象座標
      this.cacheCoordinate = this.leaflet
        .marker([latitude, longitude], {
          icon: this.setIconColor('blue'),
        })
        .bindPopup(`<p>${name}</p>
        <a href="https://www.google.com.tw/maps/search/${name}" target="_blank">Google 導航</a>
        `);
      // 加到地圖後，再執行 openPopup
      this.cacheCoordinate.addTo(this.myMap).openPopup();
      this.myMap.setView([latitude, longitude], 14);
      this.setTop();
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
    setTop() {
      let value = 0;
      if (window.screen.width >= 992) {
        value = 550;
      }
      window.scrollTo({
        top: value,
        behavior: 'smooth',
      });
    },
    displayMap() {
      this.myMap = this.leaflet.map('mapID', {
        center: [25.0462, 121.5174],
        zoom: 13,
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
    },
    polyLine(geo) {
      const wicket = new Wicket.Wkt();
      const geojsonFeature = wicket.read(geo).toJson();
      const myStyle = {
        color: '#fe5e01',
        weight: 5,
        opacity: 1,
      };
      if (this.myLayer) {
        this.myMap.removeLayer(this.myLayer);
      }
      this.myLayer = this.leaflet
        .geoJSON(geojsonFeature, {
          style: myStyle,
        })
        .addTo(this.myMap);
      this.myLayer.addData(geojsonFeature);
      // zoom the map to the layer
      this.myMap.fitBounds(this.myLayer.getBounds());
      this.getScenicSpotData(
        geojsonFeature.coordinates[0][0][1],
        geojsonFeature.coordinates[0][0][0],
      );
      this.getRestaurantData(
        geojsonFeature.coordinates[0][0][1],
        geojsonFeature.coordinates[0][0][0],
      );
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
  },
};
</script>
