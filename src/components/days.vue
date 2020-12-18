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
    props:["tempratureData", "selectedTemp"],
    data(){
return{

}
    },
    methods:{
        fahrenheittoCelcius(temp) {
      return parseFloat(temp - 273.15).toFixed(0);
    },
    updateChart(temp) {
      this.$emit("onDaySelect", temp)
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
<style scoped>

button {
  background: white;
}
.days {
  display: flex;
  width:100%;
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
</style>