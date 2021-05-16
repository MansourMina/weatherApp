<template>
  <div class="container-fluid px-1 px-sm-3 py-5 mx-auto">
    <div class="row d-flex justify-content-center">
      <div class="row card0" v-if="clickMethod">
        <div class="card1 col-lg-8 col-md-7">
          <div class="text-center">
            <img class="image mt-5" :src="imageWeather" width="50%" />
          </div>
          <div class="row px-3 mt-3 mb-3">
            <h1 class="large-font mr-3">{{ currentWeather }}</h1>
            <div class="d-flex flex-column mr-3">
              <h2 class="mt-3 mb-0">{{ countryName }}</h2>
              <small> {{ timeofCity }}</small>
            </div>
            <div class="d-flex flex-column text-center">
              <h3 class="fa fa-sun-o mt-4"></h3>
              <p class="display-5">{{ description }}</p>
            </div>
          </div>
        </div>
        <div class="card2 col-lg-4 col-md-5">
          <div class="row px-3">
            <input
              type="text"
              name="location"
              placeholder="Another location"
              class="mb-5"
              v-model="searchCountry"
              @keyup.enter="getWeather()"
            />
          </div>
          <div class="mr-5">
            <p>Weather Details</p>
            <div class="d-flex px-3">
              <p class="light-text mr-2">Feels Like</p>
              <p style="margin-left: 20px;">{{ feelsLike }}</p>
            </div>
            <div class="d-flex px-3">
              <p class="light-text">Humidity</p>
              <p style="margin-left: 20px;">{{ humidity }}</p>
            </div>
            <div class="d-flex px-3">
              <p class="light-text">Sunrise</p>
              <p style="margin-left: 20px;">{{ sunrise }}</p>
            </div>
            <div class="d-flex px-3">
              <p class="light-text">Sunset</p>
              <p style="margin-left: 20px;">{{ sunset }}</p>
            </div>
            <div class="d-flex px-3">
              <p class="light-text">Temperature max</p>
              <p style="margin-left: 20px;">{{ tmax }}</p>
            </div>
            <div class="d-flex px-3">
              <p class="light-text">Temperature min</p>
              <p style="margin-left: 20px;">{{ tmin }}</p>
            </div>
          </div>
        </div>
      </div>
      <div class="row card0" v-else>
        <div class="card1 col-lg-8 col-md-7">
          <div class="d-flex flex-column text-center">
            <h3 class="fa fa-sun-o mt-4"></h3>
            <p class="display-5">{{ message }}</p>
          </div>
        </div>
        <div class="card2 col-lg-4 col-md-5">
          <div class="row px-3">
            <input
              type="text"
              name="location"
              placeholder="Another location"
              class="mb-5"
              v-model="searchCountry"
              @keyup.enter="getWeather()"
            />
          </div>
          <div class="mr-5">
            <p>Weather Details</p>
            <div class="d-flex px-3">
              <p class="light-text mr-2">Feels Like</p>
              <p style="margin-left: 20px;">{{ feelsLike }}</p>
            </div>
            <div class="d-flex px-3">
              <p class="light-text">Humidity</p>
              <p style="margin-left: 20px;">{{ humidity }}</p>
            </div>
            <div class="d-flex px-3">
              <p class="light-text">Sunrise</p>
              <p style="margin-left: 20px;">{{ sunrise }}</p>
            </div>
            <div class="d-flex px-3">
              <p class="light-text">Sunset</p>
              <p style="margin-left: 20px;">{{ sunset }}</p>
            </div>
            <div class="d-flex px-3">
              <p class="light-text">Temperature max</p>
              <p style="margin-left: 20px;">{{ tmax }}</p>
            </div>
            <div class="d-flex px-3">
              <p class="light-text">Temperature min</p>
              <p style="margin-left: 20px;">{{ tmin }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import axios from 'axios';
export default {
  name: 'HelloWorld',
  data() {
    return {
      currentWeather: null,
      openweatherKey: process.env.VUE_APP_OW_KEY,
      timeZoneKey: process.env.VUE_APP_TZ_KEY,
      countryName: null,
      imageWeather: null,
      tmax: null,
      tmin: null,
      sunrise: null,
      sunset: null,
      feelsLike: null,
      humidity: null,
      weatherStatus: null,
      searchCountry: '',
      lat: null,
      long: null,
      message: 'Please search for a city',
      description: null,
      timeofCity: null,
      clickMethod: false,
      arrayOfWeekdays: [
        'Sunday',
        'Monday',
        'Tuesday',
        'Wednesday',
        'Thursday',
        'Friday',
        'Saturday',
      ],
      arrayOfMonths: [
        'January',
        'February',
        'March',
        'April',
        'May',
        'June',
        'July',
        'August',
        'September',
        'October',
        'November',
        'December',
      ],
    };
  },
  created() {
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition((position) => {
        this.long = position.coords.longitude;
        this.lat = position.coords.latitude;
        // console.log(this.long);
      });
    }
   
  },

  methods: {
    async getWeather() {
      const api = `https://api.openweathermap.org/data/2.5/weather?q=${this.searchCountry}&appid=${this.openweatherKey}`;

      const response = await fetch(api);
      if (!response.ok) {
        this.clickMethod = false;
        this.message = 'City not found';
      } else {
        const json = await response.json();
        // console.log(json);
        const temperature = Math.ceil(json.main.temp - 273.15) + '°';
        let dateTime = new Intl.DateTimeFormat(
          [],
          this.getTime(
            await this.getTimeZone(json.coord.lon, json.coord.lat),
            'dateTime',
          ),
        );
        let sunriseSunset = new Intl.DateTimeFormat(
          [],
          this.getTime(
            await this.getTimeZone(json.coord.lon, json.coord.lat),
            'sunriseSunset',
          ),
        );
        // let regionNames = new Intl.DisplayNames(['en'], { type: 'region' });
        this.countryName = json.name;
        this.currentWeather = temperature;
        this.description = json.weather[0].main;
        let weatherIcon = json.weather[0].icon;
        this.feelsLike = Math.ceil(json.main.feels_like - 273.15) + '°';
        this.tmax = Math.ceil(json.main.temp_max - 273.15) + '°';
        this.tmin = Math.ceil(json.main.temp_min - 273.15) + '°';
        this.sunrise = sunriseSunset.format(new Date(json.sys.sunrise * 1000));
        this.sunset = sunriseSunset.format(new Date(json.sys.sunset * 1000));
        this.humidity = json.main.humidity + '%';
        this.imageWeather = `https://openweathermap.org/img/wn/${weatherIcon}@2x.png`;
        this.clickMethod = true;

        this.timeofCity = dateTime.format(new Date());
        // console.log(this.convertInTime(timeofCity));

        // console.log(this.description);
      }
    },
    // timeNow() {
    //   var d = new Date();
    //   let hours = d.getHours();
    //   let minutes = d.getMinutes();
    //   if (minutes < 10 && hours < 10)
    //     return `0${d.getHours()}:0${d.getMinutes()}`;
    //   if (hours < 10) return `0${d.getHours()}:${d.getMinutes()}`;
    //   if (minutes < 10) return `${d.getHours()}:0${d.getMinutes()}`;
    //   return `${d.getHours()}:${d.getMinutes()}`;
    // },
    // getDate() {
    //   var dateObj = new Date();
    //   var month = dateObj.getMonth() + 1; //months from 1-12.
    //   var day = dateObj.getDate();
    //   var year = dateObj.getFullYear();
    //   return `${day} ${this.arrayOfMonths[month - 1]} '${year}`;
    // },
    convertInTime(d) {
      let hours = d.getHours() - 6;
      let minutes = d.getMinutes();
      if (minutes < 10 && hours < 10)
        return `0${d.getHours()}:0${d.getMinutes()}`;
      if (hours < 10) return `0${d.getHours()}:${d.getMinutes()}`;
      if (minutes < 10) return `${d.getHours()}:0${d.getMinutes()}`;
      return `${d.getHours()}:${d.getMinutes()}`;
    },
    async getTimeZone(long, lat) {
      const api = `https://api.timezonedb.com/v2.1/get-time-zone?key=${this.timeZoneKey}&format=json&by=position&lat=${lat}&lng=${long}`;

      const response = await fetch(api);
      if (!response.ok) {
        console.log('etwas schiefgelaufen');
      }
      const json = await response.json();
      return json.zoneName;
    },
    getTime(zoneCity, type) {
      if (type == 'dateTime') {
        let options = {
          timeZone: zoneCity,
          month: 'numeric',
          day: 'numeric',
          year: 'numeric',
          hour: 'numeric',
          minute: 'numeric',
        };
        return options;
      }
      if (type == 'sunriseSunset') {
        let options = {
          timeZone: zoneCity,
          hour: 'numeric',
          minute: 'numeric',
        };
        return options;
      }
    },
  },
};
</script>

<style>
body {
  color: #fff;
  overflow-x: hidden;
  height: 100%;
  background-image: linear-gradient(#000000, #000000);
  background-repeat: no-repeat;
}

.card0 {
  width: 94%;
}

.card1 {
  padding: 30px 20px 30px 50px;
}

.image {
  width: 100px;
  height: 100px;
}

.large-font {
  font-size: 70px;
  font-family: Arial;
}

.fa-sun-o {
  font-size: 22px;
}

.card2 {
  background-color: #607d8b;
  padding: 0px 0px 40px 40px;
}

input {
  background-color: #607d8b;
  padding: 24px 0px 12px 0px !important;
  width: 80%;
  box-sizing: border-box;
  border: none !important;
  border-bottom: 1px solid #cfd8dc !important;
  font-size: 16px !important;
  color: #fff !important;
}

input:focus {
  -moz-box-shadow: none !important;
  -webkit-box-shadow: none !important;
  box-shadow: none !important;
  border-bottom: 1px solid #fff !important;
  outline-width: 0;
  font-weight: 400;
}

::placeholder {
  color: #b0bec5 !important;
  opacity: 1;
}

:-ms-input-placeholder {
  color: #b0bec5 !important;
}

::-ms-input-placeholder {
  color: #b0bec5 !important;
}

.light-text {
  color: #b0bec5;
}

.suggestion:hover {
  color: #fff;
  cursor: pointer;
}

.line {
  height: 1px;
  background-color: #b0bec5;
}

@media screen and (max-width: 500px) {
  .card1 {
    padding-left: 26px;
  }
}
</style>
