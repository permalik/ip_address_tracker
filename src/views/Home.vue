<template>
  <main>
    <h1>
      IP Address Tracker
    </h1>
    <input
      placeholder="Input IP Address"
      type="text"
      v-model="findIP"
    />
    <button @click="getInfo">X</button>
    <Info
      v-bind:info="info"
      v-if="info"
    />
  </main>
  <div>
    <div id="ipMap"></div>
  </div>
</template>

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
    const findIP = ref("");
    const info = ref(null);
    const tileURL = "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png";
    const attribution = "&copy; <a href=\"https://www.openstreetmap.org/copyright\">OpenStreetMap</a> contributors";
    onMounted(() => {
      mapComponent = leaflet.map("ipMap").setView([37.3230, -122.0322], 10);
      marker = leaflet.marker([37.3230, -122.0322]).addTo(mapComponent);
      popup = leaflet.popup().setLatLng([37.3230, -122.0322]).setContent('Cupertino, California');
      marker.bindPopup(popup);
      marker.on("mouseover", function(event) {
        marker.openPopup();
      });
      marker.on("mouseout", function(event) {
        marker.closePopup();
      });
      leaflet.tileLayer(
        tileURL,
        {
          attribution: attribution
        }
      )
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
        popup = leaflet.popup().setLatLng([info.value.lat, info.value.lng]).setContent(`${info.value.city}, ${info.value.state}`);
        marker.bindPopup(popup);
        marker.on("mouseover", function(event) {
          marker.openPopup();
        });
        marker.on("mouseout", function(event) {
          marker.closePopup();
        });

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