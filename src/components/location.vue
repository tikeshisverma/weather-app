<template>
  <div class="container">
    <div class="location">
      <img class="pin" src="../assets/pin.svg" alt="" />
      <input
        class="searchlocation"
        v-model="location"
        placeholder="search your location"
        ref="autocomplete"
      />
      <img class="search" src="../assets/search.svg" alt="" />
      <!-- <img class="searchIcon" src="../assets/icons8-search-48.png"> -->
      <p></p>
    </div>
  </div>
</template>
<script>
export default {
  name: "location",
  props: {
    msg: String,
  },
  data() {
    return {
      location: "",
      autoComplete: null,
    };
  },
  mounted() {
    //if(window.google){
    this.autocomplete = new window.google.maps.places.Autocomplete(
      this.$refs.autocomplete,
      { types: ["geocode"] }
    );
    this.autocomplete.addListener("place_changed", () => {
      let place = this.autocomplete.getPlace();
      let lat = place.geometry.location.lat();
      let lon = place.geometry.location.lng();
      this.$emit("onSelect", { lat, lon });
    });
  },
};
</script>
<style>
.container {
  width: 100%;
  display: flex;
  justify-content: center;
}
.location {
  position: relative;
  display: flex;
  width: 100%;
  margin: 2% 4%;
      margin-top: 40px;
}
.location img {
  width: 30px;
  position: absolute;
  margin: 2.5%;
}
.pin {
  left: 0;
}
.search {
  right: 0;
}
.marker {
  position: absolute;
  width: 30px;
  height: 30px;
  left: 23px;
  top: 10px;
}
.searchlocation {
  border: none;
  box-shadow: 0 15px 30px 5px rgba(0, 0, 0, 0.3);
  height: 60px;
  width: 100%;
  border-radius: 10px;
  padding: 0 10%;
}

.searchIcon {
  position: absolute;
  width: 19px;
  height: 19px;
  top: 15px;
  left: 205px;
}
.container {
  width: 100%;
}
</style>