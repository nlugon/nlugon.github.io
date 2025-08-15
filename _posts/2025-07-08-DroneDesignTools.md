---
layout: post
title: Drone Design Tools
date: 2025-07-08 10:00:00
description: Drone Flight Time Calculator
tags: drones tools flight-time battery
categories: drone-tools
thumbnail: assets/img/drone-design-1.png
published: true
chart:
  chartjs: true
---

This first and simplistic tool simulates flight time across battery capacities, given multiple drone design parameters. More to come.

---

### Input Parameters


<form id="droneForm">
<style>
  .grid-container {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 24px;
  }

  .grid-item {
    display: flex;
    flex-direction: column;
  }

  .grid-item label {
    font-weight: 600;
    margin-bottom: 4px;
  }

  input[type=number] {
    padding: 6px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }
</style>

<div class="grid-container">
  <div class="grid-item">
    <label>Energy Density (Wh/kg):</label>
    <input type="number" id="energy_density" value="200" step="10" oninput="plotFlightTime()">
  </div>
  <div class="grid-item">
    <label>C-Rating:</label>
    <input type="number" id="c_rating" value="25" step="1" oninput="plotFlightTime()">
  </div>
  <div class="grid-item">
    <label>Voltage per Cell (V):</label>
    <input type="number" id="v_per_cell" value="3.7" step="0.1" oninput="plotFlightTime()">
  </div>
  <div class="grid-item">
    <label>Number of Cells (S):</label>
    <input type="number" id="num_cells" value="4" step="1" oninput="plotFlightTime()">
  </div>
  <div class="grid-item">
    <label>Propeller Diameter (inches):</label>
    <input type="number" id="prop_diam" value="5" step="0.1" oninput="plotFlightTime()">
  </div>
  <div class="grid-item">
    <label>Propeller Pitch (inches):</label>
    <input type="number" id="prop_pitch" value="4.5" step="0.1" oninput="plotFlightTime()">
  </div>
  <div class="grid-item">
    <label>Motor KV (RPM/Volt):</label>
    <input type="number" id="kv" value="900" step="10" oninput="plotFlightTime()">
  </div>
  <div class="grid-item">
    <label>Dry Mass of Drone (kg):</label>
    <input type="number" id="dry_mass" value="1.2" step="0.1" oninput="plotFlightTime()">
  </div>
</div>
</form>

---

### Flight Time vs Battery Capacity

<canvas id="flightChart" width="600" height="400"></canvas>

<script>
function estimateThrustPerMotor(kv, voltage, prop_diam, prop_pitch) {
  const rpm = kv * voltage;
  const diameter_mm = prop_diam * 25.4;
  const thrust = 4e-15 * Math.pow(rpm, 2) * Math.pow(diameter_mm, 4.3) * Math.pow(prop_pitch, -1.3);
  return thrust / 9.81;
}

function plotFlightTime() {
  const energyDensity = parseFloat(document.getElementById("energy_density").value);
  const cRating = parseFloat(document.getElementById("c_rating").value);
  const voltagePerCell = parseFloat(document.getElementById("v_per_cell").value);
  const numCells = parseInt(document.getElementById("num_cells").value);
  const propDiam = parseFloat(document.getElementById("prop_diam").value);
  const propPitch = parseFloat(document.getElementById("prop_pitch").value);
  const kv = parseFloat(document.getElementById("kv").value);
  const dryMass = parseFloat(document.getElementById("dry_mass").value);

  const totalVoltage = voltagePerCell * numCells;
  const thrustPerMotor = estimateThrustPerMotor(kv, totalVoltage, propDiam, propPitch);
  const totalThrust = thrustPerMotor * 4;

  const batteryCapacities = [...Array(50).keys()].map(i => (i + 5) * 100);
  const flightTimes = [];

  batteryCapacities.forEach(capacity => {
    const energyWh = (capacity / 1000) * totalVoltage;
    const batteryMass = energyWh / energyDensity;
    const totalMass = dryMass + batteryMass;

    const powerPerKg = 150;
    const totalPower = totalMass * powerPerKg;
    const timeHours = energyWh / totalPower;
    const timeMinutes = timeHours * 60;

    flightTimes.push(timeMinutes);
  });

  const ctx = document.getElementById('flightChart').getContext('2d');
  if (window.flightChartInstance) window.flightChartInstance.destroy();

  window.flightChartInstance = new Chart(ctx, {
    type: 'line',
    data: {
      labels: batteryCapacities,
      datasets: [{
        label: 'Flight Time (minutes)',
        data: flightTimes,
        borderColor: 'rgba(0, 123, 255, 1)',
        backgroundColor: 'rgba(0, 123, 255, 0.1)',
        fill: true,
        tension: 0.3
      }]
    },
    options: {
      responsive: true,
      plugins: {
        legend: { position: 'top' },
        title: { display: true, text: 'Flight Time vs Battery Capacity' }
      },
      scales: {
        x: { title: { display: true, text: 'Battery Capacity (mAh)' } },
        y: { title: { display: true, text: 'Flight Time (minutes)' }, min: 0 }
      }
    }
  });
}

// Plot immediately on page load
document.addEventListener("DOMContentLoaded", plotFlightTime);
</script>