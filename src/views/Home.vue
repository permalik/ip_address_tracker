<template>
  <div class="container">
    <main>
      <h1>IP Address Tracker</h1>
      <input placeholder="Input IP Address" type="text" v-model="findIP" />
      <button @click="getInfo">X</button>
      <Info v-bind:info="info" v-if="info" />
    </main>
    <div>
      <div id="ipMap"></div>
    </div>
  </div>
</template>

<style scoped>
.container {

}
</style>

<script>
import Info from "../components/Info.vue";
import axios from "axios";
import leaflet from "leaflet";
import { onMounted, ref } from "vue";

export default {
  name: "Home",
  components: { Info },
  setup() {
    let mapComponent;
    let marker;
    let popup;
    let initialLat;
    let initialLng;
    let initialIP;
    let initialInfo;
    const findIP = ref("");
    const info = ref(null);
    const tileURL = "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png";
    const attribution =
      '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors';
    const getInitialIP = async () => {
      try {
        const ipData = await axios.get("https://api.ipify.org?format=json");
        initialIP = ipData.data.ip;
        console.log(initialIP);
        return initialIP;
      } catch (e) {
        console.log(e);
      }
    };
    const getInitialLocation = async () => {
      let ip;
      ip = await getInitialIP();
      try {
        const data = await axios.get(
          `https://geo.ipify.org/api/v2/country,city?apiKey=at_Qj8nFi0OaT5ACiI2ySdWdTdl3ULkk&ipAddress=${ip}`
        );
        console.log(data.data);
        let result = data.data;
        initialInfo = {
          lat: result.location.lat,
          lng: result.location.lng
        };
        initialLat = initialInfo.lat;
        initialLng = initialInfo.lng;
        let location = {
          lat: initialLat,
          lng: initialLng
        };
        console.log(initialLat);
        console.log(initialLng);
        return location;
      } catch (error) {
        console.log(error);
      }
    };
    onMounted(async () => {
      let initialLocation = await getInitialLocation();
      let lat, lng;
      lat = initialLocation.lat;
      lng = initialLocation.lng;
      mapComponent = leaflet.map("ipMap").setView([lat, lng], 10);
      marker = leaflet.marker([lat, lng]).addTo(mapComponent);
      popup = leaflet
        .popup()
        .setLatLng([lat, lng])
        .setContent("Cupertino, California");
      marker.bindPopup(popup);
      marker.on("mouseover", function() {
        marker.openPopup();
      });
      marker.on("mouseout", function() {
        marker.closePopup();
      });
      leaflet
        .tileLayer(tileURL, {
          attribution: attribution
        })
        .addTo(mapComponent);
    });

    const getInfo = async () => {
      try {
        const data = await axios.get(
          `https://geo.ipify.org/api/v2/country,city?apiKey=at_Qj8nFi0OaT5ACiI2ySdWdTdl3ULkk&ipAddress=${findIP.value}`
        );
        const result = data.data;
        info.value = {
          address: result.ip,
          city: result.location.city,
          isp: result.isp,
          lat: result.location.lat,
          lng: result.location.lng,
          state: result.location.region,
          timezone: result.location.timezone
        };
        mapComponent.setView([info.value.lat, info.value.lng], 10);
        marker.setLatLng([info.value.lat, info.value.lng]);
        popup.setLatLng([info.value.lat, info.value.lng]).setContent(`${info.value.city}, ${info.value.state}`);

      } catch (error) {
        alert("Invalid IP");
      }
    };
    return { findIP, getInfo, info };
  }
};
</script>

<style>
#ipMap {
  height: 80vh;
}
</style>
