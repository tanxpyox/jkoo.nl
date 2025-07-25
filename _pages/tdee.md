---
permalink: /tdee
title: "Calorie Calculator"
layout: default
sidebar: false
author_profile: false
redirect_from:
  - /TDEE
  - /calories
  - /cal
---

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>TDEE Calculator</title>
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
    @media all and (min-width: 600px) {
      .container {
      margin-top: 30px;
      }
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
      <h1 style="margin: 0;">Calorie Calculator</h1>
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
    <div style="display: flex; gap: 1rem;">
  <div class="form-group" style="flex: 1;">
    <label for="age">Age</label>
    <input type="number" id="age" min="10" max="100" placeholder="e.g. 30">
  </div>
  <div class="form-group" style="flex: 1;">
    <label for="gender">Gender</label>
    <select id="gender">
      <option value="male" selected>Male</option>
      <option value="female">Female</option>
    </select>
  </div>
</div>
    <div class="form-group">
      <label for="activity">Activity Level</label>
      <select id="activity">
        <option value="1.2">Sedentary (little or no exercise)</option>
        <option value="1.375">Lightly active (light exercise 1–3 days/week)</option>
        <option value="1.55" selected>Moderately active (moderate exercise 3–5 days/week)</option>
        <option value="1.725">Very active (hard exercise 6–7 days/week)</option>
        <option value="1.9">Extra active (intense training or physical job)</option>
      </select> 
    </div>
    <div class="form-group">
      <label for="goal">Goal</label>
      <select id="goal">
        <option value="0" selected>Maintenance</option>
        <option value="-500">Cutting (500 kcal deficit)</option>
        <option value="500">Bulking (500 kcal surplus)</option>
      </select>
    </div>
    <div class="output">
      Recommended Intake: <span id="tdee">0</span> calories/day
    </div>
    <div class="output">
      <abbr title="Body Mass Index">BMI</abbr>: <span id="bmi">0</span>
    </div>
    <div id="bmiLabel" style="margin-top: 0.25rem; font-size: 0.9rem; color: #555;"></div>
    <div style="position: relative; height: 8px; background: #eee; border-radius: 4px; margin-top: 0.25rem;">
      <div style="position: absolute; left: 46.25%; height: 100%; width: 1px; background: #999;"></div>
      <div style="position: absolute; left: 62.5%; height: 100%; width: 1px; background: #999;"></div>
      <div style="position: absolute; left: 75%; height: 100%; width: 1px; background: #999;"></div>
      <div id="bmiBar" style="height: 100%; width: 0%; background: #ccc; border-radius: 4px;"></div>
    </div>
  </div>

  <script data-cfasync="false">
    const inputs = document.querySelectorAll('input, select');
    const output = document.getElementById('tdee');
    const bmiOutput = document.getElementById('bmi');

    function calculateTDEE() {
      const isImperial = document.getElementById('unitToggle').checked;
      let weight = parseFloat(document.getElementById('weight').value);
      if (isImperial) weight = weight / 2.20462;
      let height = parseFloat(document.getElementById('height').value);
      if (isImperial) height = height * 2.54;
      const age = parseInt(document.getElementById('age').value);
      const gender = document.getElementById('gender').value;
      const activity = parseFloat(document.getElementById('activity').value);
      const goal = parseInt(document.getElementById('goal').value);

      if (isNaN(weight) || isNaN(height) || isNaN(age)) {
        output.textContent = '—';
        bmiOutput.textContent = '—';
        return;
      }

      let bmr;
      if (gender === 'male') {
        bmr = 10 * weight + 6.25 * height - 5 * age + 5;
      } else {
        bmr = 10 * weight + 6.25 * height - 5 * age - 161;
      }

      const tdee = Math.round(bmr * activity + goal);
      output.textContent = tdee;
      const bmiBar = document.getElementById('bmiBar');
      const bmiLabel = document.getElementById('bmiLabel');
      const bmi = (weight / ((height / 100) ** 2)).toFixed(1);
      bmiOutput.textContent = bmi;
      let bmiValue = parseFloat(bmi);
      let category = '';
      let color = '';
      const percent = Math.min(100, bmiValue/40*100);

      if (bmiValue < 18.5) {
        category = 'Underweight'; color = '#00bcd4';
      } else if (bmiValue < 25) {
        category = 'Normal'; color = '#4caf50'; 
      } else if (bmiValue < 30) {
        category = 'Overweight'; color = '#ff9800'; 
      } else {
        category = 'Obese'; color = '#f44336'; 
      }
      bmiLabel.textContent = category;
      bmiBar.style.width = percent + '%';
      bmiBar.style.background = color;
    }

    inputs.forEach(i => i.addEventListener('input', calculateTDEE));
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
        calculateTDEE();
      });
      const birthDate = new Date('2000-01-27');
      const today = new Date();
      let age = today.getFullYear() - birthDate.getFullYear();
      const m = today.getMonth() - birthDate.getMonth();
      if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) {
        age--;
      }
      document.getElementById('age').value = age;
      calculateTDEE();
    });
  </script>
</body>
