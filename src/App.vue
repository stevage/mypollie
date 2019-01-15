<template lang="pug">
    #app
        #top
            h1 
                #logo
                | MyPollie
        #middle
            #sidebar
                BoundaryType
                BoundaryInfo
                
            #map-container
                Map
        #bottom

</template>

<script>
import Map from './components/Map.vue'
import BoundaryInfo from './components/BoundaryInfo.vue'
import BoundaryType from './components/BoundaryType.vue'

export default {
    name: 'app',
    components: {
        Map,
        BoundaryInfo,
        BoundaryType
    }
}
</script>

<style>
html, body {
  height: 100vh;
  width: 100%;
  margin:0;
  padding:0;
}

#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    display: flex;
    flex-direction: column;
    height:100vh;
}

#top {
    flex-basis: 80px;
    border-bottom: 1px solid grey;
    background:hsl(30, 43.5%, 91%);
    
}

#logo {
    background-image: url(./assets/galah.jpg);
    width:50px;
    height: 50px;
    float:left;
    background-size:contain;
    margin:0 20px;
}

#top h1 {
    float: left;
}

#middle {
    flex-grow: 1;
    min-height: 0; /* Crucial to making overflow scroll work */
    display: flex;
}

#map-container {
    flex-grow:1;
    flex-basis:400px;
    position:relative;
    height:100%;
}

#sidebar {
    flex-basis: 200px; /* Horizontally */
    border-right: 1px solid lightgrey;
}

#bottom {
    flex-basis: 50px;
    border-top: 1px solid lightgrey;
}

</style>
