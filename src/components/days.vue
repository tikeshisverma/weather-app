<template>
     <div class="days">
        <button
          
          v-for="(dailyTemp, index) in tempratureData.daily"
          :class="[ dailyTemp.dt==selectedTemp.dt?'active':'','day']"
          :key="index"
          @click="updateChart(dailyTemp)"
        >
          <div class="day-and-temp">
            <div class="day-name">{{ getDayName(dailyTemp.dt) }}</div>
            <div class="day-temp">
              <span class="bold"
                >{{ fahrenheittoCelcius(dailyTemp.temp.max) }}°</span
              >
              <span class="light">
                {{ fahrenheittoCelcius(dailyTemp.temp.min) }}°
              </span>
            </div>
          </div>
          <img
            :src="getImageURL(dailyTemp.weather[0].icon)"
            alt="dailyTemp.weather[0].main"
          />
          <div class="day-type">
            {{ dailyTemp.weather[0].main }}
          </div>
        </button>
      </div>
</template>
<script>
export default {
    name:"days",
    props:["tempratureData"],
    data(){
return{
    selectedTemp:{},

}
    },
    methods:{
        fahrenheittoCelcius(temp) {
      return parseFloat(temp - 273.15).toFixed(0);
    },
    updateChart(temp) {
      console.log('temp -->', temp)
      this.selectedTemp = temp
      this.$emit("onDaySelect",this.selectedTemp)
    },
     getDayName(time) {
      const dateTime = new Date(0);
      dateTime.setUTCSeconds(time);
      return dateTime.toString("en-US").split(" ")[0];
    },
     getImageURL(weatherIcon) {
      return `http://openweathermap.org/img/wn/${weatherIcon}@2x.png`;
    },
    }
}
</script>