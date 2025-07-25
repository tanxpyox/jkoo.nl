---
permalink: /ffmi
layout: default
sidebar: false
author_profile: false
---

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>FFMI Calculator</title>
  <style>
    body {
      font-family: sans-serif;
      background: #f3f4f6;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      margin: 0;
    }
    .container {
      background: white;
      padding: 2rem;
      border-radius: 1rem;
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
      max-width: 600px;
      width: 100%;
    }
    h1 {
      text-align: center;
      font-size: 1.75rem;
      margin-bottom: 1.5rem;
    }
    .form-group {
      margin-bottom: 1rem;
    }
    label {
      display: block;
      font-weight: 600;
      margin-bottom: 0.25rem;
    }
    input, select {
      width: 100%;
      padding: 0.5rem;
      border-radius: 0.5rem;
      border: 1px solid #ccc;
    }
    .output {
      text-align: center;
      margin-top: 1.5rem;
      font-size: 1.25rem;
      font-weight: bold;
    }
      .switch {
      position: relative;
      display: inline-block;
      width: 80px;
      height: 22px;
    }
    .switch input {
      opacity: 0;
      width: 0;
      height: 0;
    }
    .slider {
      position: absolute;
      cursor: pointer;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      background-color: #007BFF;
      transition: 0.4s;
      border-radius: 22px;
    }
    .slider:before {
      position: absolute;
      content: "";
      height: 18px;
      width: 18px;
      left: 2px;
      bottom: 2px;
      background-color: #fff;
      transition: 0.4s;
      border-radius: 50%;
    }
    input:checked + .slider {
      background-color: #28a745;
    }
    input:checked + .slider:before {
      transform: translateX(58px);
    }
      .slider.round::after {
      content: 'Metric';
      color: white;
      font-size: 0.7rem;
      position: absolute;
      width: 100%;
      height: 100%;
      top: 0;
      left: 0;
      display: flex;
      align-items: center;
      justify-content: flex-end;
      padding-right: 20%;
      padding-left: 0;
      transition: 0.4s;
    }
    input:checked + .slider.round::after {
      content: 'Imperial';
      justify-content: flex-start;
      padding-left: 12%;
      padding-right: 0;
    }
  </style>
</head>

<body>
  <div class="container">
    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 1rem;">
      <h1 style="margin: 0;">FFMI Calculator</h1>
      <label class="switch">
        <input type="checkbox" id="unitToggle">
        <span class="slider round"></span>
      </label>
    </div>
   <div style="display: flex; gap: 1rem;">
  <div class="form-group" style="flex: 1;">
    <label for="weight">Weight (kg)</label>
    <input type="number" min="0" id="weight" placeholder="e.g. 70" value="70">
  </div>
  <div class="form-group" style="flex: 1;">
    <label for="height">Height (cm)</label>
    <input type="number" min="0" id="height" placeholder="e.g. 175" value="175">
  </div>
</div>
    <div class="form-group">
  <label for="bodyfat">Body Fat %</label>
  <input type="number" min="0" max="100" id="bodyfat" placeholder="e.g. 15" value="15">
</div>
<div class="form-group">
  <label for="gender">Gender</label>
  <select id="gender">
    <option value="male" selected>Male</option>
    <option value="female">Female</option>
  </select>
</div>
    <div class="output">
      FFMI: <span id="ffmi">—</span>
    </div>
    <div class="output">
      Normalized FFMI: <span id="normffmi">—</span>
    </div>
    <div id="bmiLabel" style="margin-top: 0.25rem; font-size: 0.9rem; color: #555;"></div>
    <div style="position: relative; height: 8px; background: #eee; border-radius: 4px; margin-top: 0.25rem;">
      <div id="bmiBar" style="height: 100%; width: 0%; background: #ccc; border-radius: 4px;"></div>
    </div>
</div>

  <script data-cfasync="false">
  const inputs = document.querySelectorAll('input, select');
  function calculateFFMI() {
    const isImperial = document.getElementById('unitToggle').checked;
    let weight = parseFloat(document.getElementById('weight').value);
    let height = parseFloat(document.getElementById('height').value);
    const bodyfat = parseFloat(document.getElementById('bodyfat').value);
    if (isImperial) {
      weight = weight / 2.20462;
      height = height * 2.54;
    }
    if (isNaN(weight) || isNaN(height) || isNaN(bodyfat)) {
      document.getElementById('ffmi').textContent = '—';
      document.getElementById('normffmi').textContent = '—';
      document.getElementById('bmiLabel').textContent = '';
      document.getElementById('bmiBar').style.width = '0%';
      return;
    }
    const heightM = height / 100;
    const leanMass = weight * (1 - bodyfat / 100);
    const ffmi = leanMass / (heightM * heightM);
    const normFFMI = ffmi + 6.1 * (1.8 - heightM);
    document.getElementById('ffmi').textContent = ffmi.toFixed(1);
    document.getElementById('normffmi').textContent = normFFMI.toFixed(1);

    const bar = document.getElementById('bmiBar');
    const label = document.getElementById('bmiLabel');
    let category = '', color = '', percent = Math.min(100, ((normFFMI - 15) / 15) * 100);
    const gender = document.getElementById('gender').value;

    if (gender === 'male') {
      if (normFFMI < 18) {
        category = 'Below Average🥀'; color = '#00bcd4';
      } else if (normFFMI < 20) {
        category = 'Average🧍‍♂️'; color = '#4caf50';
      } else if (normFFMI < 22) {
        category = 'Above Average🏋️‍♂️'; color = '#ff9800';
      } else if (normFFMI < 23) {
        category = 'Excellent⭐'; color = '#fbc02d';
      } else if (normFFMI < 26) {
        category = 'Superior💪'; color = '#ff5722';
      } else if (normFFMI < 28) {
        category = 'Suspicion of steroid use🚨'; color = '#e91e63';
      } else {
        category = 'Steroid usage likely💉'; color = '#9c27b0';
      }
    } else {
      if (normFFMI < 15) {
        category = 'Below Average🥀'; color = '#00bcd4';
      } else if (normFFMI < 17) {
        category = 'Average🧍‍♀️'; color = '#4caf50';
      } else if (normFFMI < 18) {
        category = 'Above Average🏋️‍♀️'; color = '#ff9800';
      } else if (normFFMI < 19) {
        category = 'Excellent⭐'; color = '#fbc02d';
      } else if (normFFMI < 21.5) {
        category = 'Superior💪'; color = '#ff5722';
      } else if (normFFMI < 25) {
        category = 'Suspicion of steroid use🚨'; color = '#e91e63';
      } else {
        category = 'Steroid usage likely💉'; color = '#9c27b0';
      }
    }
    label.textContent = category;
    bar.style.width = percent + '%';
    bar.style.background = color;
  }

  inputs.forEach(i => i.addEventListener('input', calculateFFMI));

  window.addEventListener('DOMContentLoaded', () => {
    const unitToggle = document.getElementById('unitToggle');
      unitToggle.addEventListener('change', () => {
        const isImperial = unitToggle.checked;
        const weightInput = document.getElementById('weight');
        const heightInput = document.getElementById('height');

        if (isImperial) {
          const weightInLbs = (parseFloat(weightInput.value) * 2.20462).toFixed(1);
          const heightInInches = (parseFloat(heightInput.value) / 2.54).toFixed(1);
          weightInput.value = weightInLbs;
          heightInput.value = heightInInches;
          weightInput.placeholder = "e.g. 154";
          heightInput.placeholder = "e.g. 69";
          document.querySelector('label[for="weight"]').textContent = 'Weight (lb)';
          document.querySelector('label[for="height"]').textContent = 'Height (in)';
        } else {
          const weightInKg = (parseFloat(weightInput.value) / 2.20462).toFixed(1);
          const heightInCm = (parseFloat(heightInput.value) * 2.54).toFixed(1);
          weightInput.value = weightInKg;
          heightInput.value = heightInCm;
          weightInput.placeholder = "e.g. 70";
          heightInput.placeholder = "e.g. 175";
          document.querySelector('label[for="weight"]').textContent = 'Weight (kg)';
          document.querySelector('label[for="height"]').textContent = 'Height (cm)';
        }
        calculateFFMI();
    });
    calculateFFMI();
  });
</script>
</body>