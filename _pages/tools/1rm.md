---
permalink: /tools/1rm
title: "One-Rep Max Calculator"
layout: default
sidebar: false
author_profile: false
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
      flex: 1;
    }
  </style>
</head>

<body>
  <div class="container">
    <div class="title">One-Rep Max Calculator</div>
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
</body>