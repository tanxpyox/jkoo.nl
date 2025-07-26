---
permalink: /tools/gradebounds
title: "Grade Boundary Calculator"
layout: default
sidebar: false
author_profile: false
---

<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Grade Boundary Finder</title>
  <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
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
      max-width: 700px;
      width: 100%;
    }   
    @media all and (min-width: 700px) {
      .container {
      margin-top: 30px;
      }
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
    .percentages {
      margin-top: 1.5rem;
    }
  </style>
</head>
<body>
  <div class="container">
  <h1>Grade Boundary Calculator</h1>
  <p>Enter student scores (comma or space separated):</p>
  <input type="text" id="scoreInput" placeholder="e.g. 45, 56, 78, 89" size="40" value = "27.9, 37.2, 38.4, 40.7, 41.2, 42.9, 44.7, 45.0, 45.0, 46.3, 46.4, 46.5, 46.7, 47.0, 47.1, 47.3, 47.7, 48.5, 48.6, 48.6, 49.0, 51.3, 51.5, 52.1, 52.9, 53.1, 53.4, 53.8, 54.5, 54.8, 55.3, 55.5, 57.3, 57.6, 57.8, 58.6, 58.9, 59.4, 60.3, 60.7, 60.8, 61.4, 61.7, 61.8, 62.6, 62.9, 63.4, 63.5, 63.7, 64.2, 64.2, 66, 66.7, 66.7, 67.0, 67.0, 67.4, 68.3, 68.4, 69.1, 69.1, 69.4, 69.6, 70.0, 70.4, 70.7, 71.9, 72.1, 73.1, 74.5, 74.5, 74.7, 75.1, 75.3, 75.6, 75.7, 76.1, 76.6, 77.8, 78, 78.7, 79.7, 80.3, 80.4, 80.5, 81.0, 81.9, 82.0, 83.2, 84.1, 84.2, 85.0, 85.4, 85.8, 85.8, 88.4, 88.8, 88.8, 91.5, 97.03"/>
  <button id="plotBtn">Plot Scores</button>

  <div id="mainControls" style="display: none;">
  <div class="percentages">
    <h2>Assign Boundaries by Percentages (Optional)</h2>
    <p>Enter percentages (e.g. 20, 30, 25, 25):</p>
    <input type="text" id="percentInput" placeholder="e.g. 20, 30, 25, 25" size="40" />
    <button id="applyPercentBoundariesBtn">Set Boundaries</button>
  </div>

  <div class="grades">
    <h2>Custom Grade Labels (Optional)</h2>
    <p>Enter labels (e.g. A, B, C, D):</p>
    <input type="text" id="gradeLabels" placeholder="e.g. A, B, C, D" size="40" />
  </div>

  <div id="plot" style="margin-top: 2rem;"></div>

  <div class="boundary-control">
    <h2>Adjust Boundaries</h2>
    <div id="sliders"></div>
    <button id="addBoundaryBtn">Add Boundary</button>
    <button id="exportBoundariesBtn">Export Grade Bands</button>
    <button id="exportGradesPerStudentBtn">Export Student Grades</button>
  </div>

  <div class="grade-bands">
    <h2>Grade Bands</h2>
    <ul id="bands"></ul>
  </div>
  </div>
  </div>
  
  <script data-cfasync="false">
    document.addEventListener("DOMContentLoaded", () => {
      document.getElementById("plotBtn").addEventListener("click", parseScores);
      document.getElementById("applyPercentBoundariesBtn").addEventListener("click", applyPercentBoundaries);
      document.getElementById("addBoundaryBtn").addEventListener("click", addBoundary);
      document.getElementById("exportBoundariesBtn").addEventListener("click", exportBoundaries);
      document.getElementById("exportGradesPerStudentBtn").addEventListener("click", exportGradesPerStudent);
    });

    let scores = [];
    let boundaries = [50];

    function getGradeLabels() {
      const input = document.getElementById("gradeLabels").value;
      const labels = input.split(/[,\s]+/).filter((l) => l);
      return labels.length > 0 ? labels : ["A", "B", "C", "D", "E", "F"];
    }

    function parseScores() {
      const input = document.getElementById("scoreInput").value;
      scores = input.split(/[\s,]+/).map(Number).filter(n => !isNaN(n)).sort((a, b) => b - a);
      if (scores.length > 0) {
        document.getElementById("mainControls").style.display = "block";
        drawPlot();
        renderSliders();
        renderBands();
      }

    }

    function applyPercentBoundaries() {
      const input = document.getElementById("percentInput").value;
      const percentages = input.split(/[\s,]+/).map(Number).filter(p => !isNaN(p) && p > 0);
      const totalPercent = percentages.reduce((a, b) => a + b, 0);
      if (scores.length === 0 || totalPercent !== 100) {
        alert("Percentages must add up to 100.");
        return;
      }
      let cumulative = 0;
      boundaries = [];
      for (let i = 0; i < percentages.length - 1; i++) {
        cumulative += percentages[i];
        const index = Math.floor((cumulative / 100) * scores.length);
        boundaries.push(scores[Math.min(index, scores.length - 1)]);
      }
      boundaries.sort((a, b) => a - b);
      drawPlot();
      renderSliders();
      renderBands();
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
        input.step = 0.1;
        input.value = b;
        input.oninput = (e) => updateBoundary(i, +e.target.value);
        input.onchange = () => renderSliders();
        const remove = document.createElement("button");
        remove.innerText = "Remove";
        remove.onclick = () => removeBoundary(i);
        container.appendChild(label);
        container.appendChild(input);
        container.appendChild(remove);
        slidersDiv.appendChild(container);
      });
    }

    function updateBoundary(index, value, commit = false) {
      // Update current
      boundaries[index] = value;
      // Enforce order: if current > next, push next; if current < previous, pull previous
      for (let i = index + 1; i < boundaries.length; i++) {
        if (boundaries[i] <= boundaries[i - 1]) {
          boundaries[i] = boundaries[i - 1] + 1;
        } else {
          break;
        }
      }
      for (let i = index - 1; i >= 0; i--) {
        if (boundaries[i] >= boundaries[i + 1]) {
          boundaries[i] = boundaries[i + 1] - 1;
        } else {
          break;
        }
      }
      drawPlot();
      renderBands();
      if (commit) renderSliders();
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

    function getGradeLetter(index, totalBands) {
      const labels = getGradeLabels();
      const reversedLabels = [...labels].slice(0, totalBands).reverse();
      return reversedLabels[index] || `G${index}`;
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
        const grade = getGradeLetter(i, edges.length - 1);
        const li = document.createElement("li");
        li.innerText = `${grade}: ${min}-${max} | ${count} students (${percent}%)`;
        ul.appendChild(li);
      }
    }

    function exportBoundaries() {
      const sorted = [...boundaries].sort((a, b) => a - b);
      const edges = [0, ...sorted, 100];
      let csvContent = "data:text/csv;charset=utf-8,Grade,Min,Max,Student Count,Percentage\n";
      const total = scores.length;
      for (let i = 0; i < edges.length - 1; i++) {
        const min = edges[i];
        const max = edges[i + 1];
        const count = scores.filter(s => s >= min && s < max).length;
        const percent = ((count / total) * 100).toFixed(1);
        const grade = getGradeLetter(i);
        csvContent += `${grade},${min}, ${max},${count},${percent}%\n`;
      }
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement("a");
      link.setAttribute("href", encodedUri);
      link.setAttribute("download", "grade_boundaries.csv");
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }

    function exportGradesPerStudent() {
      const sorted = [...boundaries].sort((a, b) => a - b);
      const edges = [0, ...sorted, 100];
      let csvContent = "data:text/csv;charset=utf-8,Student Score,Grade\n";
      const gradeMap = scores.map(score => {
        let grade = "?";
        for (let i = 0; i < edges.length - 1; i++) {
          if (score >= edges[i] && score < edges[i + 1]) {
            grade = getGradeLetter(i, edges.length - 1);
            break;
          }
        }
        return { score, grade };
      });
      const labels = getGradeLabels();
      gradeMap.forEach(({ score, grade }) => {
        csvContent += `${score},${grade}\n`;
      });
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement("a");
      link.setAttribute("href", encodedUri);
      link.setAttribute("download", "student_grades.csv");
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }
  </script>
</body>
</html>
