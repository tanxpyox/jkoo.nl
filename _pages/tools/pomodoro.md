---
permalink: /tools/pomodoro
title: "Pomodoro Timer + Tasklist"
layout: default
sidebar: false
author_profile: false
---

<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Pomodoro Timer + Tasks</title>
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --bg: #0f172a;
      --panel: #111827;
      --panel-2: #1f2937;
      --text: #e5e7eb;
      --muted: #9ca3af;
      --accent: #22c55e;
      --accent-2: #ef4444;
      --accent-3: #3b82f6;
      --shadow: 0 20px 30px rgba(0,0,0,.35);
    }
    @media (prefers-color-scheme: light) {
      :root { --bg:#f8fafc; --panel:#ffffff; --panel-2:#f1f5f9; --text:#111827; --muted:#475569; --shadow:0 12px 24px rgba(2,6,23,.08); }
    }
    * { box-sizing: border-box; }
    html, body { height: 100%; }
    body {
      margin: 0;
      font-family: Inter, ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial, "Apple Color Emoji", "Segoe UI Emoji";
      background: radial-gradient(1200px 800px at 70% -10%, rgba(34,197,94,.15), transparent 60%),
                  radial-gradient(800px 600px at 0% 110%, rgba(59,130,246,.15), transparent 60%), var(--bg);
      color: var(--text);
    }
    .container { max-width: 1100px; margin: 0 auto; padding: 24px; }
    header { display:flex; align-items:center; justify-content:space-between; gap:16px; margin-bottom: 20px; }
    .logo { display:flex; align-items:center; gap:10px; font-weight:700; letter-spacing:.2px; }
    .pill { padding: 4px 10px; border-radius: 999px; background: var(--panel-2); color: var(--muted); font-size: 12px; }

    .grid { display:grid; grid-template-columns: 1.2fr .8fr; gap: 24px; }
    @media (max-width: 920px) { .grid { grid-template-columns: 1fr; } }

    .card { background: linear-gradient(180deg, var(--panel), var(--panel-2)); border:1px solid rgba(255,255,255,.05); border-radius: 16px; box-shadow: var(--shadow); }
    .card h2 { margin:0 0 6px; font-size: 18px; }
    .card .sub { color: var(--muted); font-size: 13px; }
    .card .body { padding: 18px; }
    .row { display:flex; align-items:center; justify-content:space-between; gap:12px; }

    .timer-wrap { display:grid; grid-template-columns: 1fr; align-items:center; justify-items:center; padding: 16px; }
    .mode-tabs { display:flex; gap: 8px; margin-bottom: 14px; flex-wrap: wrap; }
    .tab { background: transparent; border:1px solid rgba(255,255,255,.08); color: var(--muted); padding:8px 12px; border-radius: 999px; cursor:pointer; font-weight:600; }
    .tab[aria-selected="true"] { background: rgba(34,197,94,.14); color: #86efac; border-color: rgba(34,197,94,.35); }
    .tab[data-mode="break"][aria-selected="true"] { background: rgba(59,130,246,.14); color:#93c5fd; border-color: rgba(59,130,246,.35); }
    .tab[data-mode="long"][aria-selected="true"] { background: rgba(239,68,68,.14); color:#fca5a5; border-color: rgba(239,68,68,.35); }

    .clock { position: relative; width: 320px; height: 320px; }
    .clock svg { width: 100%; height: 100%; display:block; }
    .time { position:absolute; inset:0; display:flex; align-items:center; justify-content:center; font-size: 48px; font-weight: 800; letter-spacing:1px; }

    .controls { display:flex; gap: 8px; margin-top: 14px; flex-wrap:wrap; justify-content:center; }
    button.primary { background: var(--accent); color: #001a0a; border:none; padding: 12px 18px; border-radius: 12px; font-weight: 800; text-transform: uppercase; letter-spacing:.6px; cursor: pointer; box-shadow: 0 10px 16px rgba(34,197,94,.25); }
    button.secondary { background: transparent; color: var(--muted); border:1px solid rgba(255,255,255,.12); padding: 10px 14px; border-radius: 12px; cursor:pointer; }
    button.icon { padding: 10px; width: 40px; height: 40px; display:grid; place-items:center; border-radius: 10px; }
    button:disabled { opacity:.6; cursor:not-allowed; }

    .settings { display:flex; gap: 10px; flex-wrap:wrap; justify-content:center; margin-top: 10px; color: var(--muted); font-size: 13px; }
    .settings label { display:flex; gap:6px; align-items:center; padding:6px 10px; border:1px solid rgba(255,255,255,.08); border-radius: 999px; }
    .settings input[type="number"] { width: 64px; background: transparent; color: var(--text); border:none; outline:none; font-weight:700; text-align:center; color-scheme: dark; }
    @media (prefers-color-scheme: light) {
      .settings input[type="number"] { color-scheme: light; }
    }

    .task-actions { display:flex; gap: 8px; align-items: center; }
    .task-input { display:flex; gap:8px; }
    .task-input input[type="text"] { flex:1; padding: 10px 12px; border-radius: 12px; border:1px solid rgba(255,255,255,.12); background: transparent; color: var(--text); }
    .task-input button { padding: 10px 14px; border-radius: 12px; border:none; background: var(--accent-3); color:#001026; font-weight:700; cursor:pointer; }

    .task-list { list-style:none; padding:0; margin: 12px 0 0; max-height: 420px; overflow:auto; }
    .task { display:flex; gap:10px; align-items:center; justify-content:space-between; padding: 12px; border-bottom:1px dashed rgba(255,255,255,.08); }
    .task:last-child { border-bottom:none; }
    .task .left { display:flex; align-items:center; gap:10px; flex: 1; }
    .task .title { font-weight:600; }
    .task .title.done { text-decoration: line-through; color: var(--muted); }
    .chip { font-size: 12px; padding: 2px 8px; border-radius: 999px; border:1px solid rgba(255,255,255,.12); color: var(--muted); }
    .empty { text-align:center; color: var(--muted); padding: 16px 0; }

    footer { margin-top: 24px; color: var(--muted); font-size: 12px; text-align:center; }
    kbd { background: var(--panel-2); padding:2px 6px; border-radius:6px; border:1px solid rgba(255,255,255,.12); font-weight:700; font-size: 11px; }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <div class="logo" aria-label="Pomodoro Timer">
        <svg width="28" height="28" viewBox="0 0 24 24" fill="none" aria-hidden="true"><path d="M12 8v5l3 2" stroke="currentColor" stroke-width="2" stroke-linecap="round"/></svg>
        <div>Pomodoro <span class="pill">Timer + Tasks</span></div>
      </div>
      <div class="pill">Shortcuts: <kbd>Space</kbd> start/pause · <kbd>R</kbd> reset · <kbd>N</kbd> new task</div>
    </header>

    <div class="grid">
      <section class="card" aria-labelledby="timer-title">
        <div class="body timer-wrap">
          <div class="mode-tabs" role="tablist" aria-label="Modes">
            <button class="tab" role="tab" data-mode="work" aria-selected="true">Focus</button>
            <button class="tab" role="tab" data-mode="break">Short Break</button>
            <button class="tab" role="tab" data-mode="long">Long Break</button>
          </div>

          <div class="clock" aria-live="polite">
            <svg viewBox="0 0 120 120" aria-hidden="true">
              <circle cx="60" cy="60" r="54" stroke="rgba(255,255,255,.08)" stroke-width="8" fill="none"/>
              <circle id="progress" cx="60" cy="60" r="54" stroke="var(--accent)" stroke-width="8" fill="none" stroke-linecap="round" transform="rotate(-90 60 60)" stroke-dasharray="339.292" stroke-dashoffset="339.292"/>
            </svg>
            <div class="time" id="time">25:00</div>
          </div>

          <div class="controls">
            <button id="toggle" class="primary" aria-pressed="false">Start</button>
            <button id="reset" class="secondary">Reset</button>
            <button id="skip" class="secondary">Skip</button>
            <button id="sound" class="icon secondary" title="Sound on/off" aria-pressed="true">🔔</button>
            <button id="notify" class="icon secondary" title="Desktop notifications">📢</button>
          </div>

          <div class="settings" aria-label="Settings">
            <label>Focus <input id="workMin" type="number" min="1" max="120" value="25"> min</label>
            <label>Short <input id="breakMin" type="number" min="1" max="60" value="5"> min</label>
            <label>Long <input id="longMin" type="number" min="1" max="90" value="15"> min</label>
            <label><input id="autoNext" type="checkbox" checked> Auto next</label>
          </div>
        </div>
      </section>

      <section class="card" aria-labelledby="tasks-title">
        <div class="body">
          <div class="row">
            <div>
              <h2 id="tasks-title">Tasks</h2>
              <div class="sub">Attach a task to this focus session</div>
            </div>
            <div class="task-actions">
              <span class="chip" id="sessionCount">0 sessions today</span>
              <button class="secondary" id="clearCompleted">Clear done</button>
            </div>
          </div>

          <div class="task-input" style="margin-top:10px">
            <input id="newTask" type="text" placeholder="New task (press Enter)" aria-label="New task" />
            <button id="addTask">Add</button>
          </div>

          <ul id="taskList" class="task-list" aria-live="polite"></ul>
          <div id="emptyState" class="empty">No tasks yet — add one above 👆</div>
        </div>
      </section>
    </div>

    <footer>
      Built with ❤️. Your timer and tasks persist locally. Tip: click a task's 🎯 to assign it to the current session.
    </footer>
  </div>

  <script>
    ;(() => {
      const $ = (s, el=document) => el.querySelector(s);
      const $$ = (s, el=document) => [...el.querySelectorAll(s)];

      const timeEl = $('#time');
      const toggleBtn = $('#toggle');
      const resetBtn = $('#reset');
      const skipBtn = $('#skip');
      const soundBtn = $('#sound');
      const notifyBtn = $('#notify');
      const progress = $('#progress');
      const tabs = $$('.tab');

      const workMin = $('#workMin');
      const breakMin = $('#breakMin');
      const longMin = $('#longMin');
      const autoNext = $('#autoNext');

      const taskList = $('#taskList');
      const newTask = $('#newTask');
      const addTask = $('#addTask');
      const clearCompleted = $('#clearCompleted');
      const sessionCount = $('#sessionCount');
      const emptyState = $('#emptyState');

      const PERIM = 2 * Math.PI * 54;

      const store = {
        get: (k, f) => { try { return JSON.parse(localStorage.getItem(k)) ?? f } catch { return f } },
        set: (k, v) => localStorage.setItem(k, JSON.stringify(v)),
      };

      const state = store.get('pomostate', {
        mode: 'work',
        running: false,
        target: null,
        remaining: 25 * 60,
        cycles: 0,
        today: { date: new Date().toDateString(), sessions: 0 },
        sound: true,
        settings: { work: 25, break: 5, long: 15, autoNext: true },
        tasks: [],
        currentTaskId: null,
      });

      const todayStr = new Date().toDateString();
      if (state.today?.date !== todayStr) state.today = { date: todayStr, sessions: 0 };

      function renderTasks() {
        taskList.innerHTML = '';
        emptyState.style.display = state.tasks.length ? 'none' : 'block';
        state.tasks.forEach(t => {
          const li = document.createElement('li');
          li.className = 'task';
          li.dataset.id = t.id;
          li.innerHTML = `
            <div class="left">
              <input type="checkbox" ${t.done? 'checked':''} aria-label="Complete task">
              <span class="title ${t.done? 'done':''}">${escapeHtml(t.title)}</span>
            </div>
            <div style="display:flex; gap:6px; align-items:center">
              <button class="secondary icon assign" title="Assign to current session">🎯</button>
              <button class="secondary icon up" title="Move up">⬆️</button>
              <button class="secondary icon down" title="Move down">⬇️</button>
              <button class="secondary icon del" title="Delete">🗑️</button>
            </div>`;
          if (state.currentTaskId === t.id) {
            li.style.background = 'linear-gradient(90deg, rgba(34,197,94,.1), transparent)';
          }
          taskList.appendChild(li);
        });
        sessionCount.textContent = `${state.today.sessions} session${state.today.sessions===1?'':'s'} today`;
        store.set('pomostate', state);
      }

      function escapeHtml(s){ return s.replace(/[&<>"']/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;','\'':'&#39;'}[c])); }

      function addTaskFromInput(){
        const title = newTask.value.trim();
        if (!title) return;
        state.tasks.push({ id: crypto.randomUUID(), title, done:false, createdAt: Date.now() });
        newTask.value = '';
        renderTasks();
      }

      addTask.addEventListener('click', addTaskFromInput);
      newTask.addEventListener('keydown', (e)=>{ if(e.key==='Enter'){ addTaskFromInput(); }});

      taskList.addEventListener('click', (e)=>{
        const li = e.target.closest('.task');
        if(!li) return;
        const id = li.dataset.id;
        const idx = state.tasks.findIndex(t=>t.id===id);
        if(idx<0) return;
        const t = state.tasks[idx];
        if (e.target.matches('input[type="checkbox"]')) { t.done = e.target.checked; }
        if (e.target.closest('.del')) { state.tasks.splice(idx,1); }
        if (e.target.closest('.up') && idx>0) { const tmp = state.tasks[idx-1]; state.tasks[idx-1]=t; state.tasks[idx]=tmp; }
        if (e.target.closest('.down') && idx<state.tasks.length-1) { const tmp = state.tasks[idx+1]; state.tasks[idx+1]=t; state.tasks[idx]=tmp; }
        if (e.target.closest('.assign')) { state.currentTaskId = t.id; }
        renderTasks();
      });

      clearCompleted.addEventListener('click', ()=>{
        state.tasks = state.tasks.filter(t=>!t.done);
        if(!state.tasks.some(t=>t.id===state.currentTaskId)) state.currentTaskId = null;
        renderTasks();
      });

      function setMode(mode){
        state.mode = mode;
        const mins = mode==='work'? state.settings.work : (mode==='break'? state.settings.break : state.settings.long);
        state.remaining = mins * 60;
        state.running = false;
        state.target = null;
        progress.style.stroke = mode==='work'? 'var(--accent)' : (mode==='break'? 'var(--accent-3)' : 'var(--accent-2)');
        tabs.forEach(t=>t.setAttribute('aria-selected', t.dataset.mode===mode));
        updateTimeText();
        drawProgress();
        setToggleLabel();
        store.set('pomostate', state);
      }

      function secondsToMMSS(s){ const m = Math.floor(s/60); const ss = s%60; return `${m.toString().padStart(2,'0')}:${ss.toString().padStart(2,'0')}`; }

      function updateTimeText(){ timeEl.textContent = secondsToMMSS(Math.max(0, Math.round(state.remaining))); document.title = `${timeEl.textContent} • Pomodoro`; }

      function drawProgress(){
        const total = (state.mode==='work'? state.settings.work : state.mode==='break'? state.settings.break : state.settings.long) * 60;
        const pct = 1 - (state.remaining / total);
        progress.setAttribute('stroke-dasharray', PERIM.toFixed(3));
        progress.setAttribute('stroke-dashoffset', (PERIM * (1 - pct)).toFixed(3));
      }

      function setToggleLabel(){ toggleBtn.textContent = state.running? 'Pause' : 'Start'; toggleBtn.setAttribute('aria-pressed', state.running? 'true':'false'); }

      function tick(){
        if(!state.running) return;
        const now = Date.now();
        state.remaining = Math.max(0, Math.round((state.target - now)/1000));
        updateTimeText(); drawProgress();
        if (state.remaining <= 0) { onPhaseEnd(); }
      }

      function onPhaseEnd(){
        state.running = false; setToggleLabel();
        playBeep();
        if (Notification && Notification.permission === 'granted') new Notification(`${labelForMode(state.mode)} finished`, { body: nextModeMessage(), silent: true });
        if (state.mode==='work') { state.today.sessions++; if (state.currentTaskId) bumpTaskPomodoro(state.currentTaskId); }
        sessionCount.textContent = `${state.today.sessions} session${state.today.sessions===1?'':'s'} today`;
        if (state.settings.autoNext) {
          const next = nextMode();
          setMode(next);
          start();
        }
        store.set('pomostate', state);
      }

      function labelForMode(m){ return m==='work'? 'Focus' : m==='break'? 'Short Break' : 'Long Break'; }
      function nextMode(){ return state.mode==='work'? 'break' : (state.mode==='break'? 'work' : 'work'); }
      function nextModeMessage(){ const nm = nextMode(); return `Up next: ${labelForMode(nm)}`; }

      function playBeep(){
        if(!state.sound) return;
        const ctx = new (window.AudioContext || window.webkitAudioContext)();
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.connect(gain);
        gain.connect(ctx.destination);
        osc.frequency.value = 800;
        gain.gain.setValueAtTime(0.3, ctx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.01, ctx.currentTime + 0.5);
        osc.start(ctx.currentTime);
        osc.stop(ctx.currentTime + 0.5);
      }

      function start(){
        if(state.running) return;
        state.running = true;
        state.target = Date.now() + state.remaining*1000;
        setToggleLabel();
        store.set('pomostate', state);
      }
      function pause(){ state.running = false; setToggleLabel(); store.set('pomostate', state); }

      toggleBtn.addEventListener('click', ()=>{ state.running? pause(): start(); });
      resetBtn.addEventListener('click', ()=> setMode(state.mode));
      skipBtn.addEventListener('click', ()=> { state.remaining = 0; onPhaseEnd(); });

      tabs.forEach(t=> t.addEventListener('click', ()=> setMode(t.dataset.mode)) );

      function syncInputs(){
        workMin.value = state.settings.work; 
        breakMin.value = state.settings.break; 
        longMin.value = state.settings.long; 
        autoNext.checked = !!state.settings.autoNext; 
        soundBtn.setAttribute('aria-pressed', state.sound? 'true':'false'); 
        soundBtn.textContent = state.sound? '🔔' : '🔕';
      }

      [workMin, breakMin, longMin, autoNext].forEach(inp => {
        inp.addEventListener('change', () => {
          state.settings.work = clampInt(workMin.value, 1, 120);
          state.settings.break = clampInt(breakMin.value, 1, 60);
          state.settings.long = clampInt(longMin.value, 1, 90);
          state.settings.autoNext = !!autoNext.checked;
          if (state.mode==='work' || state.mode==='break' || state.mode==='long') setMode(state.mode);
          store.set('pomostate', state);
        });
      });

      soundBtn.addEventListener('click', ()=>{ state.sound = !state.sound; syncInputs(); store.set('pomostate', state); });

      function updateNotifyButton(){
        if (!('Notification' in window)) {
          notifyBtn.textContent = '🔕';
          notifyBtn.title = 'Notifications not supported';
        } else if (Notification.permission === 'granted') {
          notifyBtn.textContent = '🔔';
          notifyBtn.title = 'Notifications enabled';
        } else if (Notification.permission === 'denied') {
          notifyBtn.textContent = '🔕';
          notifyBtn.title = 'Notifications blocked';
        } else {
          notifyBtn.textContent = '📢';
          notifyBtn.title = 'Click to enable notifications';
        }
      }

      notifyBtn.addEventListener('click', async ()=>{
        if (!('Notification' in window)) return alert('Notifications not supported.');
        await Notification.requestPermission();
        updateNotifyButton();
      });

      function clampInt(v,min,max){ v=parseInt(v,10); if(isNaN(v)) v=min; return Math.min(max, Math.max(min, v)); }

      function bumpTaskPomodoro(id){ const t = state.tasks.find(t=>t.id===id); if(!t) return; t.pomos = (t.pomos||0)+1; }

      document.addEventListener('keydown', (e)=>{
        if (e.target.matches('input, textarea')) return;
        if (e.code==='Space'){ e.preventDefault(); state.running? pause(): start(); }
        if (e.key.toLowerCase()==='r'){ setMode(state.mode); }
        if (e.key.toLowerCase()==='n'){ newTask.focus(); }
      });

      (function init(){
        syncInputs();
        setMode(state.mode);
        if (state.running && state.target) {
          const remaining = Math.max(0, Math.round((state.target - Date.now())/1000));
          state.remaining = remaining; 
          if (remaining <= 0) onPhaseEnd(); 
          else start();
        }
        renderTasks();
        setInterval(tick, 250);
        toggleBtn.addEventListener('click', ()=>{ 
          if ('Notification' in window && Notification.permission==='default') 
            Notification.requestPermission(); 
        }, { once:true });
      })();

      document.addEventListener('visibilitychange', ()=>{ 
        if(state.running && state.target){ 
          state.remaining = Math.max(0, Math.round((state.target - Date.now())/1000)); 
          updateTimeText(); 
          drawProgress(); 
        }
      });

      window.addEventListener('beforeunload', ()=> store.set('pomostate', state));
    })();
  </script>
</body>
</html>