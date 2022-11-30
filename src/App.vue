<template>
   <div id="map"></div>
</template>

<script>
import "leaflet/dist/leaflet.css"
import leaflet from 'leaflet'
import '@geoman-io/leaflet-geoman-free';  
import '@geoman-io/leaflet-geoman-free/dist/leaflet-geoman.css'; 
//  import * as turf from '@turf/turf'
import axios from 'axios'


export default {
  name: 'App',
  components: {
    
  },
  data() {
    return {
      map: null,
      polygon: null,
      marker: null,
      polygon2: null,
      options: {
          position: 'topleft', // toolbar position, options are 'topleft', 'topright', 'bottomleft', 'bottomright'
          drawMarker: true, // adds button to draw markers
          drawPolyline: true, // adds button to draw a polyline
          drawRectangle: true, // adds button to draw a rectangle
          drawPolygon: true, // adds button to draw a polygon
          drawCircle: true, // adds button to draw a cricle
          cutPolygon: true, // adds button to cut a hole in a polygon
          editMode: true, // adds button to toggle edit mode for all layers
          removalMode: true, // adds a button to remove layers
        },
        geojsonFeature: {
          // "type": "FeatureCollection",
          // "features": [
          //   {
          //     "type": "Feature",
          //     "properties": {},
          //     "geometry": {
          //       "coordinates": 
          //         [51.509, -0.08],
          //       "type": "Point"
          //     }
          //   },
          // ]
        },
        geoFeature: 0,
    }
  },
  methods: {
      newGeo(){
        var fg = leaflet.featureGroup();
            this.map.eachLayer((layer)=>{
              if(layer instanceof leaflet.Path || layer instanceof leaflet.Marker){
                fg.addLayer(layer);
              }
              });
               console.log(fg.toGeoJSON());
              return fg.toGeoJSON()
      }
  },
  mounted() {
    this.map = leaflet.map('map').setView([49.25578,28.46221], 15); 
    leaflet.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
}).addTo(this.map);
    this.polygon = leaflet.polygon([
    [49.25578,28.46221],
    [49.25724,28.46273],
    [49.25742,28.46215],
    [49.25884,28.46237],
    [49.25893,28.46130],
    [49.26026,28.46135],
    [49.26047,28.45471],
    [49.25916,28.45445],
    [49.25903,28.45517],
    [49.25837,28.45510],
    [49.25821,28.45663],
    [49.25791,28.45665],
    [49.25769,28.46030],
    [49.25685,28.46009],
    [49.25646,28.45980]
]).addTo(this.map)
  .bindPopup("P'yatnychanske cemetery")
  // .openPopup()
  .setStyle({color:'black',fillColor: 'black',});

  // this.marker = leaflet.marker().addTo(this.map);

  this.map.pm.addControls(this.options);

  // this.polygon2 = leaflet.geoJSON(this.geojsonFeature).addTo(this.map)
  // .setStyle({ color: '#000',fillColor: '#BADA55',fillOpacity: 0.5})
  },
  created() {
        axios.get('Cemetery_Vinnytska.geojson')
        .then(responce => {
            this.geojsonFeature = responce.data
            // console.log(this.geojsonFeature);
            this.polygon2 = leaflet.geoJSON(this.geojsonFeature,
            // {style: function (feature) {
            // return {color: feature.properties.color};
            // }}
            ).addTo(this.map)
            .bindPopup(function (layer) {if(layer.feature.properties.codename !== null &&  layer.feature.properties.City !== null) return layer.feature.properties.ADMIN_1+ ' область, '
            + layer.feature.properties.ADMIN_2 + ' район, ' + layer.feature.properties.ADMIN_3 + ' громада, '
            + layer.feature.properties.codename + ' ' + layer.feature.properties.City;
            else return layer.feature.properties.ADMIN_1+ ' область, '
            + layer.feature.properties.ADMIN_2 + ' район, ' + layer.feature.properties.ADMIN_3 + ' громада'
            })
        })
        .catch(el => {
            this.errors.push(el)
        })
    },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#map { height: 480px;}
</style>
