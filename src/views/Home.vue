<template>
  <div class="container">
    <main>
      <header>
        <h1>IP Address Tracker</h1>
        <div class="searchbar">
          <input placeholder="Search for any IP address or domain" type="text"
                 v-model="findIP" />
          <button @click="getInfo">
            <img alt="chevron-right" src="/chevron-right.svg" />
          </button>
        </div>
        <Info v-bind:info="info" v-if="info" />
      </header>
      <div>
        <div id="ipMap"></div>
      </div>
    </main>
  </div>
</template>

<style scoped>
header {
  align-items: center;
  background-image: url("/pattern-bg.png");
  background-repeat: no-repeat;
  background-size: cover;
  display: flex;
  flex-flow: column nowrap;
  height: 30vh;
  justify-content: center;
  position: relative;
}

h1 {
  margin-block-end: var(--margin-bottom);
}

.searchbar {
  align-items: center;
  display: flex;
  height: 60px;
  justify-content: center;
  margin-block-end: var(--margin-bottom);
  min-width: 350px;
  width: 40%;
}

input {
  border: none;
  border-radius: 15px 0 0 15px;
  font-size: clamp(15px, 1.5vw, 1.25em);
  height: inherit;
  padding: 10px 25px;
  width: 100%;
}

button {
  background: var(--grey-900);
  border: none;
  border-radius: 0 15px 15px 0;
  height: inherit;
  width: 60px;
}

#ipMap {
  height: 70vh;
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
    let initialCity;
    let initialState;
    let initialIP;
    let initialInfo;
    const findIP = ref("");
    const info = ref(null);
    const tileURL = "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png";
    const attribution =
      "&copy; <a href=\"https://www.openstreetmap.org/copyright\">OpenStreetMap</a> contributors";
    const getInitialIP = async () => {
      try {
        const ipData = await axios.get("https://api.ipify.org?format=json");
        initialIP = ipData.data.ip;
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
        let result = data.data;
        initialInfo = {
          city: result.location.city,
          lat: result.location.lat,
          lng: result.location.lng,
          state: result.location.region
        };
        initialCity = initialInfo.city;
        initialLat = initialInfo.lat;
        initialLng = initialInfo.lng;
        initialState = initialInfo.state;
        let location = {
          city: initialCity,
          lat: initialLat,
          lng: initialLng,
          state: initialState
        };
        return location;
      } catch (error) {
        console.log(error);
      }
    };
    onMounted(async () => {
      let initialLocation = await getInitialLocation();
      let city, lat, lng, state;
      city = initialLocation.city;
      lat = initialLocation.lat;
      lng = initialLocation.lng;
      state = initialLocation.state;
      mapComponent = leaflet.map("ipMap").setView([lat, lng], 10);
      marker = leaflet.marker([lat, lng]).addTo(mapComponent);
      popup = leaflet
        .popup()
        .setLatLng([lat, lng])
        .setContent(`${city}, ${state}`);
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