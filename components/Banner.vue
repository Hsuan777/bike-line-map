<template>
  <div class="" />
</template>
<script>
export default {
  data() {
    return {
      nowCategory: {
        name: '景點',
        nameEn: 'ScenicSpot',
      },
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
      categories: [
        {
          name: '景點',
          nameEn: 'ScenicSpot',
        },
        {
          name: '餐飲',
          nameEn: 'Restaurant',
        },
        {
          name: '旅宿',
          nameEn: 'Hotel',
        },
        {
          name: '活動',
          nameEn: 'Activity',
        },
      ],
      cacheSearch: '',
    };
  },
  watch: {
    cacheSearch() {
      this.$emit('searchData', this.cacheSearch);
    },
  },
  methods: {
    changeCity(city) {
      this.$emit('changeCity', city);
    },
    changeCategory(category) {
      const tempData = this.categories.filter((item) => item.nameEn === category);
      this.nowCategory = { ...tempData[0] };
      this.$emit('changeCategory', this.nowCategory);
    },
  },
};
</script>
