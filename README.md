<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>新版呈現方式模擬:固定課表 + 學生姓名比對</title>
<style>
  * { box-sizing: border-box; }
  body {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "PingFang TC", "Microsoft JhengHei", Roboto, sans-serif;
    background: #f5f6f7;
    color: #222;
  }

  #banner {
    background: #fff3cd;
    border-bottom: 2px solid #f0d98c;
    color: #6b5900;
    padding: 12px 16px;
    font-size: 12px;
    position: sticky;
    top: 0;
    z-index: 50;
  }
  #banner b { display:block; font-size: 13px; margin-bottom: 2px; }

  .page { max-width: 720px; margin: 0 auto; padding: 16px 16px 60px; }

  .card {
    background: #fff;
    border-radius: 14px;
    padding: 18px 18px 20px;
    margin-bottom: 18px;
    box-shadow: 0 1px 4px rgba(0,0,0,.06);
  }
  .card h2 { margin: 0 0 4px; font-size: 16px; }
  .card .sub { margin: 0 0 14px; font-size: 12px; color: #888; line-height: 1.5; }

  .dayTabs { display: flex; gap: 6px; margin-bottom: 14px; flex-wrap: wrap; }
  .dayTab {
    flex: 1 1 auto;
    min-width: 40px;
    padding: 8px 4px;
    border-radius: 8px;
    border: 1px solid #e0e0e0;
    background: #f5f6f7;
    font-size: 12px;
    text-align: center;
    cursor: pointer;
  }
  .dayTab .cnt { display:block; font-size: 10px; color: #06913c; margin-top:2px; }
  .dayTab.active { background: #222; border-color: #222; color: #fff; }
  .dayTab.active .cnt { color: #9be7c4; }
  .dayTab.hasSlots:not(.active) { border-color: #06C755; }

  .slotGrid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; margin-bottom: 4px; }
  .slotBtn {
    padding: 9px 4px;
    border-radius: 8px;
    border: 1px solid #e0e0e0;
    background: #fff;
    font-size: 12px;
    text-align: center;
    cursor: pointer;
    user-select: none;
    line-height: 1.3;
  }
  .slotBtn small { display:block; font-size: 9px; margin-top: 2px; }
  .slotBtn.weekly { background: #06C755; border-color: #06C755; color: #fff; font-weight: 700; }
  .slotBtn.booked { background: #2e7dfa; border-color: #2e7dfa; color: #fff; font-weight: 700; cursor: default; }

  .saveBtn {
    background: #06C755; color: #fff; border: none; border-radius: 10px;
    padding: 10px 22px; font-size: 14px; font-weight: 700; cursor: pointer; margin-top: 6px;
  }

  .legendRow { display:flex; flex-wrap:wrap; gap: 12px; font-size: 11px; color: #666; margin-top: 12px; }
  .legendRow span { display:inline-flex; align-items:center; gap:5px; }
  .dot { width: 10px; height: 10px; border-radius: 3px; display:inline-block; }

  .noteBox {
    font-size: 11px; color: #666; background: #f5f6f7; border-radius: 10px;
    padding: 10px 14px; margin-top: 14px; line-height: 1.6;
  }

  /* ---------- Admin: folder tabs ---------- */
  .adminNote { font-size: 12px; color:#666; margin-bottom: 14px; line-height:1.6; }
  .chip { display:inline-block; padding: 3px 8px; border-radius: 6px; font-size: 11px; white-space:nowrap; margin: 2px 2px 0 0; }
  .chip.booked { background:#e8effe; color:#2554d1; border:1px solid #c3d3fb; }
  .chip.open { background:#e6f9ee; color:#06913c; border:1px solid #b8ecd0; }

  .folderTabs {
    display: flex;
    gap: 4px;
    overflow-x: auto;
    padding: 0 2px;
    -webkit-overflow-scrolling: touch;
  }
  .folderTab {
    flex: 0 0 auto;
    padding: 10px 16px 9px;
    border-radius: 10px 10px 0 0;
    background: #eceef0;
    color: #777;
    font-size: 13px;
    font-weight: 600;
    cursor: pointer;
    white-space: nowrap;
    border: 1px solid #e0e0e0;
    border-bottom: none;
    position: relative;
    top: 1px;
  }
  .folderTab .cnt {
    display: inline-block;
    margin-left: 6px;
    font-size: 10px;
    font-weight: 700;
    padding: 1px 6px;
    border-radius: 10px;
    background: #dfe3e6;
    color: #888;
  }
  .folderTab.active {
    background: #fff;
    color: #222;
    border-color: #06C755;
    box-shadow: 0 -2px 0 #06C755 inset;
  }
  .folderTab.active .cnt { background: #e6f9ee; color: #06913c; }

  .folderPanel {
    background: #fff;
    border: 1px solid #e0e0e0;
    border-radius: 0 10px 10px 10px;
    padding: 16px;
  }
  .folderPanel .panelTitle { font-size: 14px; font-weight: 700; margin: 0 0 4px; }
  .folderPanel .panelSub { font-size: 12px; color: #888; margin: 0 0 14px; }

  .openDayRow { display: flex; align-items: flex-start; gap: 10px; padding: 8px 0; border-top: 1px solid #f2f2f2; }
  .openDayRow:first-of-type { border-top: none; }
  .openDayRow .dName { flex: 0 0 44px; font-size: 12px; font-weight: 700; color: #555; padding-top: 4px; }
  .openDayRow .dChips { flex: 1; display: flex; flex-wrap: wrap; gap: 4px; }
  .openDayRow .dNone { font-size: 11px; color: #ccc; padding-top: 4px; }

  .emptyState { text-align: center; color: #999; font-size: 12px; padding: 24px 10px; }

  #toast {
    position: fixed; left:50%; bottom: 24px; transform: translateX(-50%);
    background:#333; color:#fff; padding:10px 16px; border-radius:8px; font-size:13px;
    opacity:0; transition: opacity .25s; pointer-events:none; white-space:nowrap; max-width: 90%; text-align:center;
  }
  #toast.show { opacity: 1; }
</style>
</head>
<body>

<div id="banner">
  <b>🔎 設計提案模擬 v2:固定課表 + 自動帶入學生姓名(已依你的回饋拿掉「近期異動」)</b>
  因為老師大多是固定時間、固定學生,系統改成:老師只設定一次「每週固定課表」,
  跟 Google 行事曆比對後,已經有預約學生的時段會直接顯示學生姓名。
</div>

<div class="page">

  <!-- ================= 老師畫面:我的固定課表 ================= -->
  <div class="card">
    <h2>My Weekly Schedule</h2>
    <p class="sub">These are the times you teach every week. Slots already matched to a booking on the calendar show the student's name automatically.</p>

    <div class="dayTabs" id="dayTabs"></div>
    <div class="slotGrid" id="templateGrid"></div>

    <div class="legendRow">
      <span><i class="dot" style="background:#2e7dfa;"></i>Booked (student matched from calendar)</span>
      <span><i class="dot" style="background:#06C755;"></i>My usual time (no student matched yet)</span>
      <span><i class="dot" style="background:#fff;border:1px solid #e0e0e0;"></i>Not scheduled</span>
    </div>

    <button class="saveBtn" id="saveTemplateBtn">Save Weekly Schedule</button>

    <div class="noteBox">
      這一版拿掉了「近期異動」的區塊——因為機率不高,如果老師哪天臨時請假或調課,
      建議還是直接在 LINE 跟你反映,你再手動去 Google 行事曆調整那一天的事件即可,
      不需要為了少數情況把系統弄複雜。這個畫面只保留「每週固定會怎麼上課」這件事。
    </div>
  </div>

  <!-- ================= 管理後台看到的效果(示意) ================= -->
  <div class="card">
    <h2>管理後台會看到的效果(示意)</h2>
    <p class="adminNote">
      改成「資料夾標籤」的形式——上面一排是老師的名字,每個都是獨立的標籤,點開才會看到那位老師的行程,
      不會像之前那樣所有老師擠在同一張表格裡。而且點開後<b style="color:#06913c;">只顯示這位老師目前還有空、可以安排新學生的時段</b>,
      已經有學生的時段不會列出來,你要找空檔給新學生時一眼就能看完。
    </p>
    <div class="folderTabs" id="folderTabs"></div>
    <div class="folderPanel" id="folderPanel"></div>
  </div>

</div>

<div id="toast"></div>

<script>
  // ==========================================================
  // 假資料 / 邏輯(純示意,不代表最終程式碼寫法)
  //
  // 概念:TEMPLATE 存老師每個星期幾固定會上課的時段;
  // 每個時段如果曾經比對到 Google 行事曆上的事件,就會帶上 student 姓名
  // (比對方式比照現有系統:事件標題/描述裡有出現學生姓名 → 判定是這個時段的學生)。
  // 沒有 student 的時段,代表老師自己勾選「這個時間我平常有空」,但還沒配對到學生。
  // ==========================================================
  function pad(n) { return n < 10 ? '0' + n : '' + n; }

  const DAY_KEYS = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];

  function slotList() {
    const out = [];
    for (let m = 9 * 60; m < 21 * 60; m += 30) out.push(m);
    return out;
  }
  function hhmm(m) { return pad(Math.floor(m / 60)) + ':' + pad(m % 60); }
  function label(m) { return hhmm(m) + '-' + hhmm(m + 30); }

  // 老師的固定課表:星期幾 -> [{ start: 分鐘數, student: 學生姓名或 null }]
  const TEMPLATE = {
    Mon: [{ start: 14 * 60, student: 'Wayne' }],
    Tue: [],
    Wed: [{ start: 14 * 60, student: 'Irene' }, { start: 14 * 60 + 30, student: null }],
    Thu: [],
    Fri: [{ start: 16 * 60, student: 'Chris' }],
    Sat: [],
    Sun: [],
  };

  // ---------------- 固定課表編輯器 ----------------
  let activeDay = 'Mon';

  function findSlot(day, m) {
    return TEMPLATE[day].find(function (s) { return s.start === m; });
  }

  function renderDayTabs() {
    const wrap = document.getElementById('dayTabs');
    wrap.innerHTML = '';
    DAY_KEYS.forEach(function (k) {
      const btn = document.createElement('div');
      const cnt = TEMPLATE[k].length;
      btn.className = 'dayTab' + (k === activeDay ? ' active' : '') + (cnt > 0 ? ' hasSlots' : '');
      btn.innerHTML = k + (cnt > 0 ? '<span class="cnt">' + cnt + '</span>' : '');
      btn.addEventListener('click', function () { activeDay = k; renderDayTabs(); renderTemplateGrid(); });
      wrap.appendChild(btn);
    });
  }

  function renderTemplateGrid() {
    const grid = document.getElementById('templateGrid');
    grid.innerHTML = '';
    slotList().forEach(function (m) {
      const btn = document.createElement('div');
      btn.className = 'slotBtn';
      const slot = findSlot(activeDay, m);

      if (slot && slot.student) {
        btn.classList.add('booked');
        btn.innerHTML = label(m) + '<small>with ' + slot.student + '</small>';
        btn.addEventListener('click', function () {
          showToast('已比對到 Google 行事曆上的預約(' + slot.student + '),如需調整請直接到行事曆修改這堂課。');
        });
      } else if (slot) {
        btn.classList.add('weekly');
        btn.innerHTML = label(m) + '<small>Open</small>';
        btn.addEventListener('click', function () {
          const idx = TEMPLATE[activeDay].indexOf(slot);
          TEMPLATE[activeDay].splice(idx, 1);
          renderDayTabs();
          renderTemplateGrid();
          renderFolderTabs();
          renderFolderPanel();
        });
      } else {
        btn.textContent = label(m);
        btn.addEventListener('click', function () {
          TEMPLATE[activeDay].push({ start: m, student: null });
          renderDayTabs();
          renderTemplateGrid();
          renderFolderTabs();
          renderFolderPanel();
        });
      }
      grid.appendChild(btn);
    });
  }

  document.getElementById('saveTemplateBtn').addEventListener('click', function () {
    showToast('Weekly schedule saved — this now applies to every future week.');
  });

  function showToast(msg) {
    const t = document.getElementById('toast');
    t.textContent = msg;
    t.classList.add('show');
    setTimeout(function () { t.classList.remove('show'); }, 2600);
  }

  // ---------------- 管理後台:老師資料夾標籤 ----------------
  const OTHER_TEACHERS = {
    'John Cruz': {
      Tue: [{ start: 10 * 60, student: 'Kevin' }, { start: 10 * 60 + 30, student: null }],
      Thu: [{ start: 10 * 60, student: null }],
    },
    'Angela Reyes': {
      Wed: [{ start: 15 * 60, student: 'Tom' }],
      Fri: [{ start: 9 * 60, student: 'Sophia' }],
      Sat: [{ start: 10 * 60, student: null }],
    },
  };

  const DAY_FULL = { Mon: 'Mon', Tue: 'Tue', Wed: 'Wed', Thu: 'Thu', Fri: 'Fri', Sat: 'Sat', Sun: 'Sun' };

  function allTeachers() {
    const list = [{ name: 'Maria Santos', data: TEMPLATE }];
    Object.keys(OTHER_TEACHERS).forEach(function (name) {
      list.push({ name: name, data: OTHER_TEACHERS[name] });
    });
    return list;
  }

  function openSlotCount(data) {
    let n = 0;
    DAY_KEYS.forEach(function (k) { (data[k] || []).forEach(function (s) { if (!s.student) n++; }); });
    return n;
  }

  let activeTeacher = 'Maria Santos';

  function renderFolderTabs() {
    const wrap = document.getElementById('folderTabs');
    wrap.innerHTML = '';
    allTeachers().forEach(function (tc) {
      const tab = document.createElement('div');
      const cnt = openSlotCount(tc.data);
      tab.className = 'folderTab' + (tc.name === activeTeacher ? ' active' : '');
      tab.innerHTML = tc.name + '<span class="cnt">' + cnt + ' open</span>';
      tab.addEventListener('click', function () {
        activeTeacher = tc.name;
        renderFolderTabs();
        renderFolderPanel();
      });
      wrap.appendChild(tab);
    });
  }

  function renderFolderPanel() {
    const panel = document.getElementById('folderPanel');
    const tc = allTeachers().find(function (x) { return x.name === activeTeacher; });
    if (!tc) { panel.innerHTML = ''; return; }

    const cnt = openSlotCount(tc.data);
    let html = '<div class="panelTitle">' + tc.name + '</div>';

    if (cnt === 0) {
      html += '<div class="panelSub">Currently available times to book a new student</div>';
      html += '<div class="emptyState">目前所有固定時段都已經有學生,沒有空堂可以安排新學生。</div>';
      panel.innerHTML = html;
      return;
    }

    html += '<div class="panelSub">Currently available times to book a new student — ' + cnt + ' open slot' + (cnt === 1 ? '' : 's') + ' this week</div>';

    DAY_KEYS.forEach(function (k) {
      const openSlots = (tc.data[k] || [])
        .filter(function (s) { return !s.student; })
        .sort(function (a, b) { return a.start - b.start; });
      if (openSlots.length === 0) return; // 這位老師這天沒有空堂,不用列出來讓畫面更乾淨
      html += '<div class="openDayRow"><div class="dName">' + DAY_FULL[k] + '</div><div class="dChips">';
      openSlots.forEach(function (s) {
        html += '<span class="chip open">' + label(s.start) + '</span>';
      });
      html += '</div></div>';
    });

    panel.innerHTML = html;
  }

  // ---------------- 啟動 ----------------
  renderDayTabs();
  renderTemplateGrid();
  renderFolderTabs();
  renderFolderPanel();
</script>

</body>
</html>
