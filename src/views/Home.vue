<template>
  <div class="flex flex-col h-screen max-h-screen">
    <!-- search  -->
    <div
      class="
        z-20
        flex
        justify-center
        relative
        bg-hero-pattern bg-cover
        px-4
        pt-8
        pb-32
      "
    >
      <div class="w-full max-w-screen-sm">
        <h1 class="text-white text-center text-3xl pb-4">IP Address Tracker</h1>
        <div class="flex">
          <input
            v-model="queryIp"
            class="
              flex-1
              py-3
              px-2
              rounded-tl-md rounded-bl-md
              focus:outline-none
            "
            placeholder="Search for any IP Address or leave it empty to get your IP Info"
            type="text"
          />
          <i @click="getIpInfo()"
            class="
              cursor-pointer
              bg-black
              text-white
              px-4
              rounded-tr-md rounded-br-md
              flex
              items-center
              fas
              fa-chevron-right
            "
          ></i>
        </div>
      </div>
      <IPInfo v-if='IpInfo' v-bind:IpInfo="IpInfo"/>
    </div>

    <!-- Map -->
    <div id="mapid" class="h-full z-10"></div>
  </div>
</template>

<script>
// @ is an alias to /src
import leaflet from "leaflet";
import IPInfo from "../components/IPInfo";
import { onMounted,ref } from "vue";
import axios from "axios"
export default {
  name: "Home",
  components: {
    IPInfo,
  },
  setup() {
    const queryIp = ref("")
    const IpInfo = ref(null)
    let mymap;
    onMounted(() => {
      mymap = L.map("mapid").setView([23.810331, 90.412521], 9);
      L.tileLayer(
        "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoibWhhc2FuMDkiLCJhIjoiY2txeGhsbW9iMHZubjJucWhqaG5hd2Q2NCJ9.FVvSMcgOkFEfTRXxIuJS3g",
        {
          attribution:
            'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
          maxZoom: 18,
          id: "mapbox/streets-v11",
          tileSize: 512,
          zoomOffset: -1,
          accessToken: "pk.eyJ1IjoibWhhc2FuMDkiLCJhIjoiY2txeGhsbW9iMHZubjJucWhqaG5hd2Q2NCJ9.FVvSMcgOkFEfTRXxIuJS3g",
        }
      ).addTo(mymap);
    });
    const getIpInfo = async() => {
      try{
        // `https://geo.ipify.org/api/v1?apiKey=at_W1lP0vpUVk9L4OcQFDGZILQWbwVfP&ipAddress=${queryIp.value}`
        // http://api.ipstack.com/103.158.210.10?access_key=922246af91ba267b04d87f568ee1ca0f
        const data = await axios.get(`http://api.ipstack.com/${queryIp.value}?access_key=922246af91ba267b04d87f568ee1ca0f`);
        console.log(data.data)
        const result = data.data
        IpInfo.value = {
          address: result.ip,
          country: result.country_name,
          region: result.region_name,
          // isp: result.isp,
          lat: result.latitude,
          lng: result.longitude,
        };
        leaflet.marker([IpInfo.value.lat, IpInfo.value.lng]).addTo(mymap);
        mymap.setView([IpInfo.value.lat, IpInfo.value.lng], 13);
      }
      catch(err){
        alert(err.message)
      }
    }
    return {queryIp,IpInfo,getIpInfo}
  },
};
</script>
