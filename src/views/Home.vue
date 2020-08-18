

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <div id="map"></div>
  </div>
</template>

<style>
	body { margin: 0; padding: 0; }
	#map { position: absolute; top: 0; bottom: 0; width: 100%; }
</style>

<script>
/* global mapboxgl */

export default {
  data: function() {
    return {
      message: "Welcome to Vue.js!"
    };
  },
  mounted: function() {    
    mapboxgl.accessToken = process.env.VUE_APP_MAPBOX_KEY;
    var map = new mapboxgl.Map({
    container: 'map', // container id
    style: 'mapbox://styles/mapbox/streets-v11', // style URL
    center: [-81.6557, 30.3322], // starting position [lng, lat]
    zoom: 2 // starting zoom
    });

    var marker = new mapboxgl.Marker()
    .setLngLat([-81.6557, 30.3322])
    .addTo(map);

    var marker2 = new mapboxgl.Marker()
    .setLngLat([81.6557, -30.3322])
    .addTo(map);


    map.addControl(
      new MapboxGeocoder({
      accessToken: mapboxgl.accessToken,
      mapboxgl: mapboxgl
      })
    );

    

    var radius = 3;
    
    function pointOnCircle(angle) {
    return {
    'type': 'Point',
    'coordinates': [(Math.sin(angle) * radius) - 78.8784, (angle * radius) + 42.8864]
    };
    }
    
    map.on('load', function() {
    // Add a source and layer displaying a point which will be animated in a circle.
    map.addSource('point', {
    'type': 'geojson',
    'data': pointOnCircle(10000)
    });
    
    map.addLayer({
    'id': 'point',
    'source': 'point',
    'type': 'circle',
    'paint': {
    'circle-radius': 20,
    'circle-color': '#007cbf'
    }
    });
    
    function animateMarker(timestamp) {
    // Update the data to a new position based on the animation timestamp. The
    // divisor in the expression `timestamp / 1000` controls the animation speed.
    map.getSource('point').setData(pointOnCircle(timestamp / 500));
    
    // Request the next frame of the animation.
    requestAnimationFrame(animateMarker);
    }
    
    // Start the animation.
    animateMarker(0);
    });

    
  },
  methods: {}
};
</script>
