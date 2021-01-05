# amsterdam-maps-example

## Tile Server
https://t{s}.data.amsterdam.nl/topo_rd/{z}/{x}/{y}.png

## Leaflet
Rendered using [Leaflet](https://leafletjs.com/)

## VueJS Library
[Documentation](https://vue2-leaflet.netlify.app/)

## Quirks
The Amsterdam tile server has more accurate tiles of Amsterdam than opem street maps but the area is limited only to amsterdam. This means a proper projection needs to be done on the tile url addresses. I used [proj4 - npm](https://www.npmjs.com/package/proj4) to do this. It allows you to set the bound area for the map.