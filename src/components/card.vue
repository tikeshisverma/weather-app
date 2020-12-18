<template>
    <div class="detail-wrapper">
        <h1>
          {{this.fahrenheittoCelcius(selectedTemp.temp.day) }}°C
          <img :src="getImageURL(tempratureData.daily[0].weather[0].icon)" />
        </h1>
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
            <span class="timing">{{getTimeAMPM(selectedTemp.sunrise)}}</span>
          </div>
          <div class="sunset">
            <span class="title">Sunset</span>
            <span class="timing">{{getTimeAMPM(selectedTemp.sunset)}}</span>
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
    name:"card",
    props:["selectedTemp", "tempratureData", "chartData"],
    
    data(){
return{
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

}
    },
methods:{
 fahrenheittoCelcius(temp) {
      return parseFloat(temp - 273.15).toFixed(0);
    },
    getImageURL(weatherIcon) {
      return `http://openweathermap.org/img/wn/${weatherIcon}@2x.png`;
    },
      getTimeAMPM(time) {
      const dateTime = new Date(0);
      dateTime.setUTCSeconds(time);
      return dateTime.toLocaleString("en-US", { hour: "numeric", hour12: true, minute:"numeric" })
    },
},
}
</script>