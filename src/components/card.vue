<template>
  <div class="detail-wrapper">
    <div class="header">
      <h1>{{ this.fahrenheittoCelcius(selectedTemp.temp.day) }}°C</h1>
      <img :src="getImageURL(selectedTemp.weather[0].icon)" />
    </div>

    <div class="graph">
      <GChart type="LineChart" :data="chartData" :options="chartOptions" />
    </div>
    <div class="info-wrapper">
      <div class="info-card">
        Pressure
        <div class="light-text">{{ selectedTemp.pressure }} hpa</div>
      </div>
      <div class="info-card">
        Humidity
        <div class="light-text">{{ selectedTemp.humidity }}%</div>
      </div>
    </div>
    <div class="sun-timing">
      <div class="sunrise">
        <span class="title">Sunrise</span>
        <span class="timing">{{ getTimeAMPM(selectedTemp.sunrise) }}</span>
      </div>
      <div class="sunset">
        <span class="title">Sunset</span>
        <span class="timing">{{ getTimeAMPM(selectedTemp.sunset) }}</span>
      </div>
    </div>
    <div class="sun-status">
      <GChart
        type="LineChart"
        :data="chartDataSun"
        :options="chartOptionsSun"
      />
    </div>
  </div>
</template>
<script>
export default {
  name: "card",
  props: ["selectedTemp", "tempratureData", "chartData"],

  data() {
    return {
      chartOptions: {
        lineWidth: 3,
        hAxis: {
          gridlines: { color: "#ff0", count: 400 },
          gridlineColor: "#f00",
        },
        vAxis: { textPosition: "none", gridlines: { color: "#fff", count: 4 } },
        curveType: "function",
        pointSize: 5,
        legend: "none",
        colors: ["#3daae8"],
        width: 5500,
        height: 300,
      },
      chartDataSun: [
        ["time", "position"],
        [0, -5],
        [5, 10],
        [10, -5],
      ],
      chartOptionsSun: {
        hAxis: {
          textPosition: "none",
          gridlines: { color: "#fff", count: 4 },
          baselineColor: "#fff",
          gridlineColor: "#fff",
        },
        vAxis: { textPosition: "none", gridlines: { color: "#fff", count: 4 } },
        legend: "none",
        curveType: "function",
      },
    };
  },
  methods: {
    fahrenheittoCelcius(temp) {
      return parseFloat(temp - 273.15).toFixed(0);
    },
    getImageURL(weatherIcon) {
      return `http://openweathermap.org/img/wn/${weatherIcon}@2x.png`;
    },
    getTimeAMPM(time) {
      const dateTime = new Date(0);
      dateTime.setUTCSeconds(time);
      return dateTime.toLocaleString("en-US", {
        hour: "numeric",
        hour12: true,
        minute: "numeric",
      });
    },
  },
};
</script>

<style scoped>
.info-wrapper {
  display: flex;
  justify-content: space-between;
  margin: 3% 0;
  margin-top: 10%;
}
.header{
  display: flex;
  align-items: flex-start;
}
.header img{
  margin-top: 2%;
}
.light-text {
  font-weight: 300;
}
.info-card {
  color: black;
  width: 40%;
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
  width: 90%;
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
@media only screen and (max-width: 360px) {
.graph>div{
  margin-left: -150%!important;
}
}
</style>