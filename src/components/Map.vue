<template lang="pug">
    #super-container
        #map-div
        #crosshair
</template>

<script>
    import mapboxgl from 'mapbox-gl';
    import mapboxutils from 'mapbox-gl-utils';
    import MapboxGeocoder from '@mapbox/mapbox-gl-geocoder';
    import axios from 'axios';
    export default {
        methods: {
            setBoundaryType(type) {
                const layers = {
                    federal: 'elb-boundaries',
                    state: ['sed-boundaries', 'sed-fill']
                };
                Object.values(layers).forEach(this.map.U.hide);
                this.map.U.show(layers[type]);

            }
        },
        mounted() {
            mapboxgl.accessToken = 'pk.eyJ1Ijoic3RldmFnZSIsImEiOiJGcW03aExzIn0.QUkUmTGIO3gGt83HiRIjQw';
            const map = new mapboxgl.Map({
                container: 'map-div',
                center: [145, -37.8],
                zoom: 12,
                style: 'mapbox://styles/mapbox/light-v9',
            });
            this.map = map;
            mapboxutils.init(map);
            map.addControl(new MapboxGeocoder({
                accessToken: mapboxgl.accessToken
            }));
            map.addControl(new mapboxgl.GeolocateControl({
                positionOptions: {
                    enableHighAccuracy: true
                },
                trackUserLocation: true
            }));
            map.U.onLoad(() => {
                initLayers(map);
                this.setBoundaryType('federal');
            });
            map.on('moveend', e => {
                const { lng, lat } = map.getCenter();
                const btype = window.BoundaryType.current;
                window.BoundaryInfo.boundary = undefined;
                if (btype === 'federal') {
                    axios.get(`https://which-boundary.glitch.me/lookup/elb/${lng},${lat}`)
                    .then(({ data }) => {
                        console.log(data);
                        window.BoundaryInfo.boundary = data;
                    });
                } else if (btype === 'state') {
                    const mapdiv = document.getElementById('map-div');
                    const f = map.queryRenderedFeatures([mapdiv.offsetWidth / 2, mapdiv.offsetHeight / 2], {
                        layers: ['sed-fill'],
                    })[0];
                    if (f) {
                        window.BoundaryInfo.boundary = { 
                            name: f.properties.SED_NAME
                        };
                    }

                }
                // console.log(e);
            });
            window.map = map;
            window.Map = this;
        }
    }
    require('@/../node_modules/mapbox-gl/dist/mapbox-gl.css');
    require('@/../node_modules/@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css');
    function initLayers(map) {
        map.U.addVector('elb', 'https://vector-tiles.terria.io/FID_COM20160509_ELB/{z}/{x}/{y}.pbf')
        .addLine('elb-boundaries', {
            sourceLayer: 'FID_COM20160509_ELB',
            lineColor: 'darkblue'

        });
        map.U.addVector('sed', 'https://vector-tiles.terria.io/FID_SED_2011_AUST/{z}/{x}/{y}.pbf')
        .addLine('sed-boundaries', {
            sourceLayer: 'FID_SED_2011_AUST',
            lineColor: 'darkred'
        })
        .addFill('sed-fill', {
            sourceLayer: 'FID_SED_2011_AUST',
            fillColor: 'transparent'
        });

        
    }
</script>

<style scoped>
#super-container {
    height: 100%;
    position:relative;
}
#map-div {
    width: 100%;
    height: 100%;
    background:grey;
    /* position:relative; */
    position:absolute;
}
#crosshair {
    position: absolute;
    top:50%;
    left: 50%;
    width: 32px;
    height: 32px;
    margin: -16px 0 0 -16px;
    /* background: black; */
    background-image: url(../assets/circular-target.png);
}
</style>