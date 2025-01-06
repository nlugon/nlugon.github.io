---
layout: post
title: Mars Geography
date: 2025-01-03 12:06:00
description: Description.
tags: planetary landings geojson maps
categories: space-exploration
map: true
published: false
geojson:
  {
    "type": "FeatureCollection",
    "crs": {
      "type": "name",
      "properties": {
        "name": "urn:ogc:def:crs:IAU2000:49900"
      }
    },
    "features": [
      {
        "type": "Feature",
        "properties": {
          "name": "Olympus Mons",
          "color": "#FF4500",
          "border_color": "#0000FF",
          "info": "Olympus Mons: The tallest volcano in the Solar System."
        },
        "geometry": {
          "type": "Point",
          "coordinates": [135.0, 18.0] 
        }
      },
      {
        "type": "Feature",
        "properties": {
          "name": "Valles Marineris",
          "color": "#FF4500",
          "border_color": "#0000FF",
          "info": "Valles Marineris: A massive canyon system on Mars."
        },
        "geometry": {
          "type": "Point",
          "coordinates": [-70.0, -14.0] 
        }
      },
      {
        "type": "Feature",
        "properties": {
          "name": "Gale Crater",
          "color": "#FF4500",
          "border_color": "#0000FF",
          "info": "Gale Crater: The landing site of NASA's Curiosity rover."
        },
        "geometry": {
          "type": "Point",
          "coordinates": [137.4, -4.6] 
        }
      }
    ]
  }
---

<div id="map-mars" style="height: 500px; width: 100%;"></div>


---



## Sources

1. [trek.nasa.gov](https://trek.nasa.gov/mars/#v=0.1&x=-16.259765321697557&y=-8.613281089331622&z=1&p=urn%3Aogc%3Adef%3Acrs%3AEPSG%3A%3A104905&d=&locale=&b=mars&e=-278.34960418278723%2C-147.83202849240539%2C245.83007353939212%2C130.60546631374214&sfz=&w=)
2. [NASA APIs](https://api.nasa.gov/mars-wmts/catalog/?utm_source=chatgpt.com)