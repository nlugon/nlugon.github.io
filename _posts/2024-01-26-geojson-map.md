---
layout: post
title: Discover a Random Remote Location
date: 2024-12-25 12:00:00
description: Click refresh to discover a new random location to visit!
tags: travel random locations
categories: blog
map: true
---

<script>
const hardcodedLocations = [
    {
        name: "Denali National Park, Alaska",
        description: "Home to North America's tallest peak, Denali offers breathtaking vistas and a vast wilderness.",
        image: "https://upload.wikimedia.org/wikipedia/commons/5/51/Mount_McKinley_and_Denali_National_Park_Road_2048px.jpg",
        coordinates: [63.1148, -151.1926]
    },
    {
        name: "Torngat Mountains National Park, Canada",
        description: "A rugged, remote wilderness where dramatic mountains meet the sea.",
        image: "https://upload.wikimedia.org/wikipedia/commons/9/92/Torngat_Mountains.jpg",
        coordinates: [58.5, -63.621]
    },
    {
        name: "Great Sand Dunes National Park, Colorado",
        description: "Explore the tallest sand dunes in North America, with stunning views of the Sangre de Cristo Mountains.",
        image: "https://upload.wikimedia.org/wikipedia/commons/4/44/Great_Sand_Dunes%2C_Colo.jpg",
        coordinates: [37.74, -105.54]
    },
    {
        name: "Auyuittuq National Park, Canada",
        description: "A pristine Arctic environment with glacial fjords and dramatic peaks.",
        image: "https://upload.wikimedia.org/wikipedia/commons/6/6e/Auyuittuq_National_Park.jpg",
        coordinates: [67.2, -65.033]
    },
    {
        name: "Yosemite National Park, California",
        description: "Famous for its waterfalls, granite cliffs, and giant sequoias, Yosemite is a must-visit destination.",
        image: "https://upload.wikimedia.org/wikipedia/commons/a/a5/Vernal_Falls_in_Yosemite_National_Park.jpg",
        coordinates: [37.8651, -119.5383]
    },
    {
        name: "Yellowstone National Park, Wyoming",
        description: "The world's first national park, featuring geysers, hot springs, and diverse wildlife.",
        image: "https://upload.wikimedia.org/wikipedia/commons/6/69/Yellowstone_Norris_Geyser_Basin_1.jpg",
        coordinates: [44.4280, -110.5885]
    },
    {
        name: "Banff National Park, Canada",
        description: "A stunning destination in the Canadian Rockies with turquoise lakes and snow-capped peaks.",
        image: "https://upload.wikimedia.org/wikipedia/commons/e/e5/Moraine_Lake_17092005.jpg",
        coordinates: [51.4968, -115.9281]
    },
    {
        name: "Zion National Park, Utah",
        description: "Known for its towering sandstone cliffs and the thrilling Angels Landing hike.",
        image: "https://upload.wikimedia.org/wikipedia/commons/f/f7/Angels_Landing.jpg",
        coordinates: [37.2982, -113.0263]
    },
    {
        name: "Acadia National Park, Maine",
        description: "A coastal paradise with rocky shores, forested trails, and beautiful vistas.",
        image: "https://upload.wikimedia.org/wikipedia/commons/3/3c/Bass_Harbor_Head_Light_2016.jpg",
        coordinates: [44.3386, -68.2733]
    },
    {
        name: "Grand Canyon National Park, Arizona",
        description: "One of the world's seven natural wonders, the Grand Canyon offers awe-inspiring views.",
        image: "https://upload.wikimedia.org/wikipedia/commons/e/e1/Grand_canyon_view_from_south_rim_2009.JPG",
        coordinates: [36.1069, -112.1129]
    }
];

function getRandomLocation() {
    return hardcodedLocations[Math.floor(Math.random() * hardcodedLocations.length)];
}

document.addEventListener("DOMContentLoaded", function () {
    const contentDiv = document.getElementById("random-location-content");
    const randomLocation = getRandomLocation();

    contentDiv.innerHTML = `
        <h2>${randomLocation.name}</h2>
        <p>${randomLocation.description}</p>
        <img src="${randomLocation.image}" alt="${randomLocation.name}" style="width:100%; height:auto; border-radius:8px;" />
        <p><a href="https://www.google.com/maps?q=${randomLocation.coordinates[0]},${randomLocation.coordinates[1]}" target="_blank">View on Google Maps</a></p>
    `;

    // Initialize Leaflet map
    const map = L.map('map').setView(randomLocation.coordinates, 5);

    // Add Esri World Imagery (satellite)
    L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
        attribution: 'Tiles © Esri — Source: Esri, Maxar, Earthstar Geographics, and the GIS User Community'
    }).addTo(map);

    // Add OpenStreetMap labels for clarity
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors',
        opacity: 0.6 // Adjust opacity for better visibility
    }).addTo(map);

    L.marker(randomLocation.coordinates).addTo(map)
        .bindPopup(`<b>${randomLocation.name}</b><br>${randomLocation.description}`).openPopup();
});
</script>

<div id="random-location-content"></div>
<div id="map" style="height: 400px; margin-top: 20px;"></div>
