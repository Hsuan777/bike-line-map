<template>
  <div>
    <figure class="banner banner__recommend magic-height-300 mb-8 mb-lg-15">
      <div class="container d-flex align-items-end h-100">
        <p class="mb-5 fz-huge fw-bolder zi-9">
          推薦行程
        </p>
      </div>
    </figure>
    <div class="container bg-secondary py-5 mb-5 rounded">
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
    </div>
    <div class="container">
      <div class="row">
        <div class="col-md-10 mx-auto">
          <ul class="list-unstyled row g-3">
            <li
              v-for="(item, index) in filterCyclingData"
              :key="item.RouteName + index"
              class="col-md-6 border border-secondary"
            >
              <div class="p-3">
                <h2>{{ item.RouteName }}</h2>
                <p>開始地點: {{ item.RoadSectionStart }}</p>
                <p>結束地點: {{ item.RoadSectionEnd }}</p>
              </div>
            </li>
          </ul>
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
    };
  },
  computed: {
    filterCyclingData() {
      return this.cyclingData.filter((item) => item.RouteName.match(this.cacheSearch));
    },
  },
  mounted() {
    this.getCyclingData('Taipei');
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
    changeCity(value) {
      this.nowCityName = value;
      this.cacheSearch = '';
      this.getCyclingData(value);
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
