<template>
    <div id="map" style="width: 100%; height: 500px"></div>
</template>

<script>
import 'leaflet/dist/leaflet.css';
require('leaflet')
console.log(L)
        
export default {
    name: 'tutorial',
    mounted(){
        this.initMap()
    },
    data(){
        return {
            geoGson: null,
            map: null
        }
    },
    methods:{
        initMap(){
            this.map = L.map('map', {
            center: [-23.4299855, -51.9169693],
            zoom: 13
        })

            this.map.createPane('labels');

            this.map.getPane('labels').style.zIndex = 650;

            this.map.getPane('labels').style.pointerEvents = 'none';

            var positron = L.tileLayer('http://{s}.basemaps.cartocdn.com/light_nolabels/{z}/{x}/{y}.png', {
                attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
            }).addTo(this.map);

            var positronLabels = L.tileLayer('http://{s}.basemaps.cartocdn.com/light_only_labels/{z}/{x}/{y}.png', {
            attribution: '©OpenStreetMap, ©CartoDB',
                    pane: 'labels'
            }).addTo(this.map);

        

            var controlLayers = L.control.layers( null, null, {
            position: "bottomright", // suggested: bottomright for CT (in Long Island Sound); topleft for Hartford region
            collapsed: false // false = open by default
            }).addTo(this.map);

            L.control.zoom({position: "topright"}).addTo(this.map);

            let url = 'https://raw.githubusercontent.com/WesleiDev/geojson-maringa-por-zona/master/marginga.geojson'

            this.$axios.get(url)
                .then(data =>{
                    console.log('DATA:', data)
                    this.geoGson = L.geoJson(data.data, {
                        style: this.style,
                        onEachFeature: this.onEachFeature  
                    }).addTo(this.map);

                    controlLayers.addOverlay(this.geoGson, 'Todos'); 
                })

            },
        style(feature) {
            return {
                fillColor: feature.properties.fill,
                weight: 2,
                opacity: 1,
                color: 'white',
                dashArray: '3',
                fillOpacity: 0.7
            };
        },
        highlightFeature(e) {
            var layer = e.target;

            layer.setStyle({
                weight: 5,
                color: '#666',
                dashArray: '',
                fillOpacity: 0.7
            });

            if (!L.Browser.ie && !L.Browser.opera && !L.Browser.edge) {
                layer.bringToFront();
            }
        },
        resetHighlight(e) {
            this.geoGson.resetStyle(e.target);
        },
        zoomToFeature(e) {
            this.map.fitBounds(e.target.getBounds());
        },
        onEachFeature(feature, layer) {
            layer.on({
                mouseover: this.highlightFeature,
                mouseout: this.resetHighlight,
                click: this.zoomToFeature
            });
        }
    }
}
</script>

<style>
    
</style>
