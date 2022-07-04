<template>
  <main v-if="!loading">
    <DataTitle :text="title" :dataDate="dataDate" />

    <DataBox :stats="stats" />

    <CountrySelect @get-country="getCountryData" :countries="countries" />

    <button @click="clearCountryData" v-if="stats.Country"
      class="bg-blue-700 text-white rounded p-3 mt-10 mb-10 focus:outline-none hover:bg-blue-600">Clear
      Country</button>
  </main>

  <main class="flex flex-col align-center justify-center text-center" v-else>
    <div class="text-gray-500 text-3xl mt-10 mb-6">
      Fetching Data
    </div>
    <img :src="loadingImage" class="w-24 m-auto" />
  </main>
</template>

<script>
// @ is an alias to /src
import CountrySelect from '@/components/CountrySelect.vue';
import DataBox from '@/components/DataBox.vue';
import DataTitle from '@/components/DataTitle.vue';

export default {
  name: 'HomeView',
  components: { DataTitle, DataBox, CountrySelect },
  data() {
    return {
      loading: true,
      title: 'Global',
      dataDate: '',
      stats: {},
      countries: {},
      loadingImage: require("../assets/hourglass.gif")
    }
  },
  methods: {
    async fetchCovidData() {
      const res = await fetch('https://api.covid19api.com/summary');
      const data = res.json();
      return data;
    },
    getCountryData(country) {
      this.stats = country;
      this.title = country.Country;
    },
    async clearCountryData() {
      this.loading = true;
      const data = await this.fetchCovidData();
      this.title = "Global";
      this.stats = data.Global;
      this.loading = false;
    }
  },
  async created() {
    const data = await this.fetchCovidData();
    this.dataDate = data.Date;
    this.stats = data.Global;
    this.countries = data.Countries;
    this.loading = false;
  }
}
</script>
