<template>
<div>
    <div style="max-width: 800px; margin: 0 auto; display: flex; align-items: center; justify-content: space-between">
      <div>
        <h1>Your coordinates:</h1>
        <p>{{ myCoordinates.lat }} Latitude, {{ myCoordinates.lng }} Longitude</p>
      </div>
  </div>

  <MglMap :accessToken="accessToken" :mapStyle="mapStyle" :center="myCoordinates" hash>
    <MglGeolocateControl position="top-right" :trackUserLocation="true" :showUserLocation="false" />
    <MglFullscreenControl />

    <!--MARKER dos usuario amigos do usuario logado.
        MARKER of the user friends of the logged in user. -->
    <MglMarker v-for="marker in markers" :key="marker.name" :coordinates="marker.coordinates">
      <!--Este slot recomendo colocar dentro de DIV, pois se colocar fora e colocar no avatar, desconfigurá o avatar e ele ficará fora das coordenadas.
          This slot I recommend putting inside DIV, because if you put it outside and put it in the avatar, it will unconfigure the avatar and it will be out of the coordinates. -->
      <div slot="marker">
        <v-avatar>
          <img src="https://randomuser.me/api/portraits/women/81.jpg" />
        </v-avatar>
      </div>

      <MglPopup :onlyText="true" anchor="top">
        <VCard>
          <div>{{marker.name}}</div>
        </VCard>
      </MglPopup>
    </MglMarker>

    <!--MARKER do Usuario logado. Ele esta abaixo dos amigos, pois, quando o zoom estiver distante, será a foto do usuario que estará no topo da pilha.
        User's MARKER logged in. It is below the friends, because when the zoom is far, it will be the user's photo that will be at the top of the stack.-->

    <!--Este "IF" é para fazer aparecer a foto do usuario somente se ele estiver com localização. Senão ao iniciar, ele fica voando no meio do oceano.
        This "IF" is for displaying the user's photo only if he is located. If not, it starts flying in the middle of the ocean. -->
    <MglMarker v-if="(myCoordinates.lat && myCoordinates.lng) != 0" :coordinates="myCoordinates">
      <div slot="marker">
        <v-avatar>
          <img src="https://avatars3.githubusercontent.com/u/43021528?s=460&u=d833cef76869dfaeacc8dbc110718c4bc8e1b02d&v=4"/>
        </v-avatar>
      </div>
      <MglPopup :coordinates="myCoordinates" :onlyText="true" anchor="top">
        <VCard>
          <div>Você</div>
        </VCard>
      </MglPopup>
    </MglMarker>
  </MglMap>
</div>
</template>
<script>
import Mapbox from "mapbox-gl";
import {
  MglMap,
  MglGeolocateControl,
  MglFullscreenControl,
  MglMarker,
  MglPopup,
} from "vue-mapbox";

export default {
  components: {
    MglMap,
    MglGeolocateControl,
    MglFullscreenControl,
    MglMarker,
    MglPopup,
  },
  data() {
    return {
      myCoordinates: {
        lat: 0,
        lng: 0,
      },
      accessToken:"YOUR_TOKEN", // your access token. Needed if you using Mapbox maps
      mapStyle: "mapbox://styles/mapbox/streets-v11", // your map style
      markers: [
        { coordinates: [-77.032, 38.913], name: "Amanda" },
        { coordinates: [-122.413184, 37.776366], name: "Scarlet" },
        { coordinates: [-54.26867, -28.28287], name: "Marcelo" },
        { coordinates: [-80.036731,26.705947], name: "Thalles" },
        { coordinates: [-80.03963, 26.70585], name: "Amanda Cerni" },
        { coordinates: [-111.549668, 39.014], name: "Chris" },
      ],
    };
  },
  created() {
    // Precisamos definir a biblioteca mapbox-gl aqui para usá-la no modelo.
    // We need to set mapbox-gl library here in order to use it in template.
    this.mapbox = Mapbox;

    // Pegas as coordenadas do usuario, da requesição do browser.
    // You take the user's coordinates, browser requirements.
    this.$getLocation({})
      .then((coordinates) => {
        console.log(coordinates, "COORDENADAS");
        this.myCoordinates = coordinates;
      })
      .catch((error) => alert(error));
  },
  methods: {
    // Esse metodo não existia ai dava falha na construção. Ele que constroe o mapa.
    // This method did not exist so there was a failure in construction. He who builds the map.
    onMapLoaded() {
      console.log("loaded");
    },
  },
};
</script>


<style>
/* Estava faltando definir o height. Tem que ter esse estilo aqui, senão o mapa é renderizado, porem não vai aparecer por falta de altura.*/
/* The height was missing. You have to have this style here, otherwise the map is rendered, but it will not appear due to lack of height. */
.mgl-map-wrapper {
  height: 630px;
}

body {
  margin: 0;
  padding: 0;
}
</style>