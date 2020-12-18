<template>
  <div id="app">
    <div class="wrapper" v-if="selectedTemp && selectedTemp.temp">
      <location msg="test" @onSelect="handleSelect" />
      <days :tempratureData="tempratureData" @onDaySelect="handleDaySelect" :selectedTemp="selectedTemp" />
      <card
        :selectedTemp="selectedTemp"
        :tempratureData="tempratureData"
        :chartData="getChartData(chartData, selectedTemp)"
      />
    </div>
  </div>
</template>

<script>
import location from "./components/location";
import days from "./components/days";
import card from "./components/card";
export default {
  name: "App",
  components: {
    location,
    days,
    card,
  },
  data() {
    return {
      initChatData: [["time", "temp", { type: "string", role: "style" }]],
      selectedTemp: {},
      currentLocation: {},
      tempreture: "",
      pressure: "",
      humidity: "",
      sunRise: "",
      sunSet: "",
      tempratureData: {},
    };
  },
  mounted() {
    this.initGPSRequest();
  },
  methods: {
    initGPSRequest(){
      if ("geolocation" in navigator) {
      navigator.geolocation.getCurrentPosition(
        (position) => {
          this.currentLocation = {
            lat: position.coords.latitude,
            lon: position.coords.longitude,
          };
          this.getWeatherData(
            this.currentLocation.lat,
            this.currentLocation.lon
          );
        },
        (error_message) => {
          this.getWeatherData(
            21.1938,
            81.3509
          );
          console.error(
            "An error has occured while retrieving location",
            error_message
          );
        }
      );
    } else {
      console.log("geolocation is not enabled on this browser");
    }
    },
    handleSelect({ lat, lon }) {
      this.getWeatherData(lat, lon);
    },
    getChartData(chartData, selectedTemp) {
      const { dt } = selectedTemp;
      const now = new Date();
      const nowInSeconds = now.getTime() / 1000 - now.getHours()*60*60;
      const oneDayInSeconds = 24 * 60 * 60;
      const twoDaysInSeconds = oneDayInSeconds * 2;
      const tomorrow = nowInSeconds + oneDayInSeconds;
      const dayAfterTomorrow = nowInSeconds + twoDaysInSeconds;
      const columnValue = chartData[0];
      let newChartData = [columnValue,['',0,'']];

      if (dt > dayAfterTomorrow) {
        return newChartData;
      } else if (dt > tomorrow && dt < dayAfterTomorrow) {
        newChartData = [columnValue, ...chartData.slice(24)];
      } else {
        newChartData = [columnValue, ...chartData.slice(1, 24)];
      }
      return newChartData
    },
    handleDaySelect(data) {
      this.selectedTemp = data;
    },
    fahrenheittoCelcius(temp) {
      return parseFloat(temp - 273.15).toFixed(0);
    },
    getImageURL(weatherIcon) {
      return `http://openweathermap.org/img/wn/${weatherIcon}@2x.png`;
    },
    getDayName(time) {
      const dateTime = new Date(0);
      dateTime.setUTCSeconds(time);
      return dateTime.toString("en-US").split(" ")[0];
    },

    formatDateTime(time, temp) {
      const dateTime = new Date(0);
      dateTime.setUTCSeconds(time);
      return `${this.fahrenheittoCelcius(temp)}°
       ${dateTime.toLocaleString("en-US", { hour: "numeric", hour12: true })}`;
    },
    getWeatherData(lat, lon) {
      fetch(
        `https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&exclude=minutely&appid=c57de956c1a2b33b5aa1dde5394753f1`
      )
        .then((response) => response.json())
        .then((res) => {
          this.tempratureData = res;
          this.selectedTemp = res.daily[0];
          this.chartData = [
            ...this.initChatData,
            ...res.hourly.map((t) => {
              return [
                this.formatDateTime(t.dt, t.temp),
                t.temp,
                "point { size: 5; shape-type: circle; fill-color: #ffffff; stroke-color:#3daae8;stroke-width:3}",
              ];
            }),
          ];
          this.pressure = res.daily.map((d) => d.pressure);
          this.humidity = res.daily.map((d) => d.humidity);
          this.sunRise = res.daily.map((s) => s.sunrise);
          this.sunSet = res.daily.map((s) => s.sunset);

          this.tempreture = this.fahrenheittoCelcius(res.current.temp);

        });
    },
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: black;
  margin-top: 60px;
}
.wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  width:80%;
  max-width: 600px;
  margin: auto;
  box-shadow: 0 15px 30px 5px rgba(0, 0, 0, 0.3);
  border-radius: 40px;
}
@media only screen and (max-width: 600px) {
.wrapper {
  box-shadow: none;
  width:100%;
}
.detail-wrapper h1{
  font-size: 4em;
}
}

</style>
