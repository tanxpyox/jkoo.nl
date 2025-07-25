---
permalink: /1rm
title: "1-Rep Max Calculator"
layout: default
sidebar: false
author_profile: false
redirect_from:
  - /1RM
---

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>1RM Estimator</title>
  <style>
    body {
      font-family: sans-serif;
      background: #f3f4f6;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
    .container {
      margin-top: 30px;
      background: white;
      padding: 2rem;
      border-radius: 1rem;
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
      max-width: 400px;
      width: 100%;
    }
    .title {
      text-align: center;
      font-size: 1.5rem;
      font-weight: bold;
      margin-bottom: 1.5rem;
    }
    .slider-group {
      margin-bottom: 1.5rem;
    }
    .label {
      display: block;
      margin-bottom: 0.5rem;
      font-weight: 500;
    }
    .output {
      text-align: center;
      font-size: 1.25rem;
      font-weight: 600;
    }
    input[type="range"] {
      appearance: none;
      -webkit-appearance: none;
      width: 100%;
      height: 6px;
      background: #ddd;
      border-radius: 3px;
      outline: none;
      transition: background 0.2s;
    }
    input[type="range"]:hover {
      background: #ccc;
    }
    input[type="range"]::-webkit-slider-thumb {
      -webkit-appearance: none;
      appearance: none;
      width: 14px;
      height: 14px;
      background: #111827;
      border-radius: 50%;
      cursor: pointer;
      transition: transform 0.2s ease, background 0.2s ease;
    }
    input[type="range"]:active::-webkit-slider-thumb {
      transform: scale(1.3);
      background: #374151;
    }
    input[type="range"]::-moz-range-thumb {
      width: 14px;
      height: 14px;
      background: #111827;
      border-radius: 50%;
      cursor: pointer;
      transition: transform 0.2s ease, background 0.2s ease;
    }
    input[type="range"]:active::-moz-range-thumb {
      transform: scale(1.3);
      background: #374151;
    }
  </style>
</head>

<body>
  <div class="container">
    <div class="title">1RM Estimator</div>
    <div class="slider-group">
      <label for="weight" class="label">Weight Lifted: <span id="weightVal">100</span></label>
      <input type="range" id="weight" min="20" max="300" value="100" />
    </div>
    <div class="slider-group">
      <label for="reps" class="label">Repetitions: <span id="repsVal">5</span></label>
      <input type="range" id="reps" min="1" max="15" value="5" />
    </div>
    <div class="output">
      Estimated 1RM: <span id="output">117</span>
    </div>
  </div>
  <script>
    const weightSlider = document.getElementById('weight');
    const repsSlider = document.getElementById('reps');
    const weightVal = document.getElementById('weightVal');
    const repsVal = document.getElementById('repsVal');
    const output = document.getElementById('output');
    function updateEstimate() {
      const w = parseInt(weightSlider.value);
      const r = parseInt(repsSlider.value);
      const estimated1RM = Math.round(w * (1 + r / 30));
      weightVal.textContent = w;
      repsVal.textContent = r;
      output.textContent = estimated1RM;
    }
    weightSlider.addEventListener('input', updateEstimate);
    repsSlider.addEventListener('input', updateEstimate);
  </script>
  <footer style="position: absolute; bottom: 1rem; width: 100%; text-align: center; color: #6b7280; font-size: 0.75rem;">
    Estimate calculated using the Epley formula: 1RM = weight × (1 + reps / 30)
  </footer>
</body>