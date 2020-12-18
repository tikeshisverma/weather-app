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
  methods: {
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
      // console.log(time)
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
          // console.log(this.chartData)
          this.pressure = res.daily.map((d) => d.pressure);
          this.humidity = res.daily.map((d) => d.humidity);
          this.sunRise = res.daily.map((s) => s.sunrise);
          this.sunSet = res.daily.map((s) => s.sunset);

          this.tempreture = this.fahrenheittoCelcius(res.current.temp);

          // console.log(res)
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
  max-width: 700px;
  margin: auto;
  box-shadow: 0 15px 30px 5px rgba(0, 0, 0, 0.3);
  border-radius: 40px;
}

button {
  background: white;
}
.days {
  display: flex;
  max-width: 600px;
  overflow: scroll;
  padding-top: 2%;
}
.day {
  border: 2px solid transparent;
  text-align: center;
}
.active {
  border: 2px solid #3daae8;
}

.day:focus {
  outline: none;
}
.day-temp .bold,
.day-name {
  font-weight: 600;
}
.day-temp .light,
.day-type {
  color: darkgray;
  font-weight: 600;
}
.day:hover .day-temp .light,
.day:hover .day-type {
  color: #e8e8e8;
}
.day:hover {
  color: white;
  background: #728f99;
}

.day-and-temp,
.day-type {
  width: 100%;
}
.day-and-temp,
.day {
  display: flex;
  flex-direction: column;
}
.info-wrapper {
  display: flex;
  justify-content: space-between;
  margin: 3% 0;
  margin-top: 10%;
}
.light-text {
  font-weight: 300;
}
.info-card {
  color: black;
  width: 170px;
  background-color: #f5fafe;
  border-radius: 4%;
  font-size: 125%;
  font-weight: 600;
  text-align: left;
  padding: 3%;
}
h1 {
  text-align: initial;
  font-size: 100px;
  color: black;
}
.graph {
  overflow-x: scroll;
  overflow-y: hidden;
}
.graph > div {
  margin-left: -71%;
}
.detail-wrapper {
  margin-left: 12%;
  margin-right: 12%;
  margin-top: 5%;
  margin-bottom: 5%;
  padding: 2%;
  border-radius: 3%;
  box-shadow: 0 15px 30px 5px rgba(0, 0, 0, 0.3);
  max-width: 600px;
}
.detail-wrapper h1 {
  margin: 2% 0;
}
.sun-status {
  width: 100%;
}
.sun-timing {
  display: flex;
  justify-content: space-between;
}
.sunrise,
.sunset {
  display: flex;
  flex-direction: column;
}
.title {
  font-weight: 600;
  color: black;
}
.timing {
  color: darkgray;
  font-weight: 600;
}
</style>
