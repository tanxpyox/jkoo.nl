---
permalink: /gradebounds
title: "Grade Boundary Finder"
layout: default
sidebar: false
author_profile: false
---

<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Grade Boundary Visualizer</title>
  <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      max-width: 700px;
      margin: auto;
      padding: 2rem;
      background: #fdfdfd;
      color: #222;
    }
    h1, h2 {
      font-weight: 500;
      margin-bottom: 1rem;
    }
    input[type="text"], button {
      padding: 0.5rem 1rem;
      font-size: 1rem;
      border: 1px solid #ccc;
      border-radius: 6px;
      background-color: white;
      margin-bottom: 1rem;
    }
    button {
      cursor: pointer;
      background-color: #eee;
    }
    button:hover {
      background-color: #ddd;
    }
    .boundary-control {
      margin-top: 2rem;
    }
    .slider-container {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      margin-bottom: 0.75rem;
    }
    .slider-container span {
      flex: 0 0 90px;
    }
    input[type="range"] {
      flex: 1;
    }
    ul {
      padding-left: 1.25rem;
    }
    li {
      margin-bottom: 0.25rem;
    }
  </style>
</head>
<body>
  <h1>Grade Boundary Assignment Tool</h1>
  <p>Enter student scores (comma or space separated):</p>
  <input type="text" id="scoreInput" placeholder="e.g. 45, 56, 78, 89" size="40" />
  <button onclick="parseScores()">Plot Scores</button>
  <div id="plot" style="margin-top: 2rem;"></div>

  <div class="boundary-control">
    <h2>Adjust Boundaries</h2>
    <div id="sliders"></div>
    <button onclick="addBoundary()">Add Boundary</button>
  </div>

  <div class="grade-bands">
    <h2>Grade Bands</h2>
    <ul id="bands"></ul>
  </div>

  <script>
    let scores = [];
    let boundaries = [50];

    function parseScores() {
      const input = document.getElementById("scoreInput").value;
      scores = input.split(/[\s,]+/).map(Number).filter(n => !isNaN(n)).sort((a, b) => a - b);
      if (scores.length > 0) {
        drawPlot();
        renderSliders();
        renderBands();
      }
    }

    function drawPlot() {
      const shapes = boundaries.map(x => ({
        type: 'line',
        x0: x, x1: x,
        y0: 0, y1: 1,
        xref: 'x', yref: 'paper',
        line: { color: '#e74c3c', width: 2, dash: 'dot' }
      }));
      Plotly.newPlot('plot', [{
        x: scores,
        type: 'histogram',
        marker: { color: 'lightblue' },
        opacity: 0.9
      }], {
        title: 'Score Histogram',
        xaxis: { title: 'Score', tickfont: { size: 12 } },
        yaxis: { title: 'Count', tickfont: { size: 12 } },
        shapes: shapes,
        paper_bgcolor: '#fdfdfd',
        plot_bgcolor: '#fdfdfd',
        font: { family: 'Segoe UI', size: 14 }
      }, {responsive: true});
    }

    function renderSliders() {
      const slidersDiv = document.getElementById("sliders");
      slidersDiv.innerHTML = "";
      boundaries.forEach((b, i) => {
        const container = document.createElement("div");
        container.className = "slider-container";

        const label = document.createElement("span");
        label.innerText = `Boundary ${i + 1}:`;

        const input = document.createElement("input");
        input.type = "range";
        input.min = Math.min(...scores);
        input.max = Math.max(...scores);
        input.step = 1;
        input.value = b;
        input.oninput = (e) => updateBoundary(i, +e.target.value);

        const remove = document.createElement("button");
        remove.innerText = "Remove";
        remove.onclick = () => removeBoundary(i);

        container.appendChild(label);
        container.appendChild(input);
        container.appendChild(remove);
        slidersDiv.appendChild(container);
      });
    }

    function updateBoundary(index, value) {
      boundaries[index] = value;
      boundaries.sort((a, b) => a - b);
      drawPlot();
      renderBands();
    }

    function addBoundary() {
      const mid = Math.floor((Math.min(...scores) + Math.max(...scores)) / 2);
      boundaries.push(mid);
      boundaries.sort((a, b) => a - b);
      renderSliders();
      drawPlot();
      renderBands();
    }

    function removeBoundary(index) {
      boundaries.splice(index, 1);
      renderSliders();
      drawPlot();
      renderBands();
    }

    function renderBands() {
      const sorted = [...boundaries].sort((a, b) => a - b);
      const edges = [0, ...sorted, 100];
      const ul = document.getElementById("bands");
      ul.innerHTML = "";
      const total = scores.length;
      for (let i = 0; i < edges.length - 1; i++) {
        const min = edges[i];
        const max = edges[i + 1];
        const count = scores.filter(s => s >= min && s < max).length;
        const percent = ((count / total) * 100).toFixed(1);
        const li = document.createElement("li");
        li.innerText = `${min}-${max}: ${count} students (${percent}%)`;
        ul.appendChild(li);
      }
    }
  </script>
</body>
</html>