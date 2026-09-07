<html lang="en">
<head>
<base target="_top">
<meta charset="UTF-8">
<title>Coconut English Scheduling</title>
<script src="https://static.line-scdn.net/liff/edge/2/sdk.js"></script>
<style>
  * { box-sizing: border-box; }
  body {
    margin: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "PingFang TC", "Microsoft JhengHei", Roboto, sans-serif;
    background: #f5f6f7;
    color: #222;
  }
  #bootStatus {
    text-align: center;
    padding: 60px 16px;
    color: #888;
    font-size: 14px;
  }

  /* ===================== Teacher view ===================== */
  #teacherRoot { display: none; padding-bottom: 90px; }

  #teacherRoot header {
    background: #06C755;
    color: #fff;
    padding: 16px;
    text-align: center;
  }
  #teacherRoot header h1 { margin: 0; font-size: 17px; }
  #teacherRoot header p { margin: 4px 0 0; font-size: 12px; opacity: .9; }

  #t_profileBar {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 10px 16px;
    background: #fff;
    border-bottom: 1px solid #e0e0e0;
  }
  #t_profileBar img { width: 32px; height: 32px; border-radius: 50%; }
  #t_profileBar span { font-size: 14px; font-weight: 600; }

  #t_status { text-align: center; padding: 40px 16px; color: #888; font-size: 14px; }

  #t_dateRow {
    display: flex;
    overflow-x: auto;
    gap: 8px;
    padding: 12px;
    background: #fff;
    border-bottom: 1px solid #e0e0e0;
    -webkit-overflow-scrolling: touch;
  }
  .dateChip {
    flex: 0 0 auto;
    padding: 8px 10px;
    border-radius: 10px;
    border: 1px solid #e0e0e0;
    background: #f5f6f7;
    font-size: 12px;
    text-align: center;
    min-width: 52px;
    cursor: pointer;
  }
  .dateChip .w { display:block; font-size: 11px; color: #888; }
  .dateChip .d { display:block; font-size: 15px; font-weight: 700; margin-top:2px; }
  .dateChip.active { background: #06C755; border-color: #06C755; color: #fff; }
  .dateChip.active .w { color: rgba(255,255,255,.85); }
  .dateChip .count { display:block; font-size: 10px; margin-top: 2px; color: #06C755; }
  .dateChip.active .count { color: #fff; }

  #t_legend {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
    padding: 0 16px 8px;
    font-size: 11px;
    color: #666;
  }
  #t_legend span { display: inline-flex; align-items: center; gap: 4px; }
  .dot { width: 10px; height: 10px; border-radius: 3px; display: inline-block; }

  #t_slotArea { padding: 16px; }
  #t_slotArea h2 { font-size: 14px; margin: 0 0 10px; color: #555; }
  .slotGrid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; }
  .slotBtn {
    padding: 10px 4px;
    border-radius: 8px;
    border: 1px solid #e0e0e0;
    background: #fff;
    font-size: 13px;
    text-align: center;
    cursor: pointer;
    user-select: none;
  }
  .slotBtn.selected { background: #06C755; border-color: #06C755; color: #fff; font-weight: 700; }
  .slotBtn.busy { background: #d9dbe0; border-color: #d9dbe0; color: #888; cursor: not-allowed; }
  .slotBtn.busy.mine { background: #4a7dff; border-color: #4a7dff; color: #fff; }
  .slotBtn small { display: block; font-size: 10px; margin-top: 2px; }

  #t_footer {
    text-align: center;
    padding: 16px;
    font-size: 10px;
    color: #bbb;
  }

  #t_submitBar {
    position: fixed;
    left: 0; right: 0; bottom: 0;
    background: #fff;
    border-top: 1px solid #e0e0e0;
    padding: 12px 16px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 12px;
  }
  #t_submitBar .summary { font-size: 12px; color: #666; }
  #t_submitBtn {
    background: #06C755;
    color: #fff;
    border: none;
    border-radius: 10px;
    padding: 12px 20px;
    font-size: 15px;
    font-weight: 700;
    cursor: pointer;
  }
  #t_submitBtn:disabled { background: #bbb; }

  #t_toast {
    position: fixed;
    left: 50%; bottom: 90px;
    transform: translateX(-50%);
    background: #333;
    color: #fff;
    padding: 10px 16px;
    border-radius: 8px;
    font-size: 13px;
    opacity: 0;
    transition: opacity .25s;
    pointer-events: none;
    white-space: nowrap;
  }
  #t_toast.show { opacity: 1; }

  /* ===================== Admin view ===================== */
  #adminRoot { display: none; }

  #adminRoot .adminHeader {
    background: #222;
    color: #fff;
    padding: 16px 20px;
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 12px;
  }
  #adminRoot .adminHeader h1 { margin: 0; font-size: 18px; }
  #adminRoot .adminHeader p { margin: 4px 0 0; font-size: 12px; color: #bbb; }
  #a_langBtn {
    flex: 0 0 auto;
    background: #444;
    color: #fff;
    border: 1px solid #666;
    border-radius: 8px;
    padding: 6px 12px;
    font-size: 12px;
    cursor: pointer;
  }

  .adminContainer { padding: 16px 20px 60px; max-width: 1100px; margin: 0 auto; }

  .adminToolbar {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    align-items: center;
    background: #fff;
    padding: 12px 16px;
    border-radius: 10px;
    margin-bottom: 14px;
    box-shadow: 0 1px 4px rgba(0,0,0,.06);
  }
  .adminToolbar label { font-size: 12px; color: #666; }
  .adminToolbar input[type=date] { padding: 6px; border: 1px solid #ddd; border-radius: 6px; }

  .adminTabs { display: flex; gap: 8px; margin-bottom: 14px; }
  .tabBtn {
    background: #fff;
    border: 1px solid #ddd;
    color: #555;
    border-radius: 8px;
    padding: 8px 16px;
    font-size: 13px;
    cursor: pointer;
  }
  .tabBtn.active { background: #222; color: #fff; border-color: #222; font-weight: 700; }

  .copyIdBtn {
    background: #f5f6f7;
    border: 1px solid #ddd;
    border-radius: 6px;
    padding: 2px 8px;
    font-size: 11px;
    cursor: pointer;
    margin-left: 6px;
  }

  .btn {
    background: #06C755;
    color: #fff;
    border: none;
    border-radius: 8px;
    padding: 10px 18px;
    font-size: 14px;
    font-weight: 700;
    cursor: pointer;
  }

  #a_legend { display:flex; flex-wrap: wrap; gap: 14px; font-size: 12px; color: #666; margin-bottom: 12px; }
  #a_legend span { display: inline-flex; align-items: center; gap: 5px; }

  .tableWrap { overflow-x: auto; border-radius: 10px; }
  #a_overviewTable, #a_teachersTable { border-collapse: collapse; width: 100%; background: #fff; border-radius: 10px; overflow: hidden; }
  #a_overviewTable th, #a_overviewTable td,
  #a_teachersTable th, #a_teachersTable td { border: 1px solid #eee; padding: 8px; vertical-align: top; font-size: 12px; }
  #a_overviewTable th, #a_teachersTable th { background: #fafafa; text-align: left; position: sticky; top: 0; }
  .teacherCell { font-weight: 700; white-space: nowrap; background: #fafafa; }

  .chip {
    display: inline-block;
    padding: 2px 6px;
    border-radius: 6px;
    font-size: 11px;
    margin: 2px 2px 0 0;
    white-space: nowrap;
  }
  .chip.free { background: #e6f9ee; color: #06913c; border: 1px solid #b8ecd0; }
  .chip.busyMine { background: #e8effe; color: #2554d1; border: 1px solid #c3d3fb; }

  #a_unmatchedPanel {
    margin-top: 20px;
    background: #fff9e6;
    border: 1px solid #f0d98c;
    border-radius: 10px;
    padding: 12px 16px;
    font-size: 12px;
    color: #6b5900;
  }
  #a_unmatchedPanel h3 { margin: 0 0 8px; font-size: 13px; }

  #a_status { text-align: center; color: #888; padding: 30px; font-size: 13px; }
  .empty { color: #ccc; font-size: 11px; }
</style>
</head>
<body>

<div id="bootStatus">Connecting to LINE...</div>

<!-- ===================== Teacher view ===================== -->
<div id="teacherRoot">
  <header>
    <h1>Teacher Availability</h1>
    <p>Select the time slots you're available to teach. Green = you're available, blue = you already have a class booked.</p>
  </header>

  <div id="t_profileBar" style="display:none;">
    <img id="t_avatar" src="" alt="">
    <span id="t_displayName"></span>
  </div>

  <div id="t_status">Loading your schedule...</div>

  <div id="t_app" style="display:none;">
    <div id="t_dateRow"></div>
    <div id="t_legend">
      <span><i class="dot" style="background:#fff;border:1px solid #e0e0e0;"></i>Not selected</span>
      <span><i class="dot" style="background:#06C755;"></i>I'm available</span>
      <span><i class="dot" style="background:#4a7dff;"></i>I have a class booked</span>
      <span><i class="dot" style="background:#d9dbe0;"></i>Already booked (unavailable)</span>
    </div>
    <div id="t_slotArea">
      <h2 id="t_slotAreaTitle"></h2>
      <div class="slotGrid" id="t_slotGrid"></div>
    </div>
    <div id="t_footer"></div>
  </div>

  <div id="t_submitBar" style="display:none;">
    <div class="summary" id="t_summary">No time slots selected yet</div>
    <button id="t_submitBtn">Submit</button>
  </div>

  <div id="t_toast"></div>
</div>

<!-- ===================== Admin view ===================== -->
<div id="adminRoot">
  <div class="adminHeader">
    <div>
      <h1 id="a_hTitle"></h1>
      <p id="a_hSubtitle"></p>
    </div>
    <button id="a_langBtn"></button>
  </div>

  <div class="adminContainer">
    <div id="a_status">Loading...</div>

    <div id="a_app" style="display:none;">
      <div class="adminTabs">
        <button class="tabBtn active" id="a_tabSchedule"></button>
        <button class="tabBtn" id="a_tabTeachers"></button>
      </div>

      <div id="a_scheduleView">
        <div class="adminToolbar">
          <label id="a_lblStart"></label> <input type="date" id="a_startDate">
          <label id="a_lblEnd"></label> <input type="date" id="a_endDate">
          <button class="btn" id="a_refreshBtn"></button>
          <span id="a_lastUpdated" style="font-size:11px;color:#999;"></span>
        </div>

        <div id="a_legend">
          <span><i class="dot" style="background:#e6f9ee;border:1px solid #b8ecd0;"></i><span id="a_legFree"></span></span>
          <span><i class="dot" style="background:#e8effe;border:1px solid #c3d3fb;"></i><span id="a_legBusy"></span></span>
        </div>

        <div class="tableWrap" id="a_tableWrap" style="display:none;">
          <table id="a_overviewTable"></table>
        </div>

        <div id="a_unmatchedPanel" style="display:none;"></div>
      </div>

      <div id="a_teachersView" style="display:none;">
        <div class="adminToolbar">
          <button class="btn" id="a_teachersRefreshBtn"></button>
          <span id="a_teachersCount" style="font-size:11px;color:#999;"></span>
        </div>
        <div class="tableWrap">
          <table id="a_teachersTable"></table>
        </div>
      </div>
    </div>
  </div>
</div>

<script>
  // ==========================================================
  // Shared bootstrap: one LIFF login, then route by role
  // ==========================================================
  const LIFF_ID = <?!= JSON.stringify(liffId) ?>;
  const CONFIG = <?!= JSON.stringify(config) ?>;

  function boot() {
    let idToken = null;
    let profile = null;

    liff.init({ liffId: LIFF_ID }).then(function () {
      if (!liff.isLoggedIn()) {
        liff.login();
        return;
      }
      idToken = liff.getIDToken();
      return liff.getProfile();
    }).then(function (p) {
      if (!p) return; // redirecting to login
      profile = p;

      google.script.run
        .withSuccessHandler(function (role) {
          document.getElementById('bootStatus').style.display = 'none';
          if (role && role.ok && role.isAdmin) {
            document.getElementById('adminRoot').style.display = 'block';
            AdminApp.init(idToken);
          } else {
            document.getElementById('teacherRoot').style.display = 'block';
            TeacherApp.init(idToken, profile, role);
          }
        })
        .withFailureHandler(function (err) {
          document.getElementById('bootStatus').textContent = 'Failed to check permissions: ' + err.message;
        })
        .getMyRole(idToken);
    }).catch(function (err) {
      document.getElementById('bootStatus').textContent = 'LINE login failed: ' + err;
    });
  }

  boot();
</script>

<script>
  // ==========================================================
  // TeacherApp
  // ==========================================================
  var TeacherApp = (function () {
    let idToken = null;
    let dates = [];
    let busyByDate = {};
    let selected = {};
    let activeDate = null;

    function pad(n) { return n < 10 ? '0' + n : '' + n; }
    function toDateStr(d) { return d.getFullYear() + '-' + pad(d.getMonth() + 1) + '-' + pad(d.getDate()); }

    function buildDateList() {
      const list = [];
      const start = new Date();
      start.setHours(0, 0, 0, 0);
      const totalDays = CONFIG.WEEKS_AHEAD * 7;
      for (let i = 0; i < totalDays; i++) {
        const d = new Date(start.getTime());
        d.setDate(start.getDate() + i);
        list.push(d);
      }
      return list;
    }

    function buildSlotsForDay() {
      const slots = [];
      const startMin = CONFIG.SLOT_START_HOUR * 60;
      const endMin = CONFIG.SLOT_END_HOUR * 60;
      for (let m = startMin; m < endMin; m += CONFIG.SLOT_MINUTES) {
        slots.push({ startMin: m, endMin: m + CONFIG.SLOT_MINUTES });
      }
      return slots;
    }

    function minToHHMM(m) { return pad(Math.floor(m / 60)) + ':' + pad(m % 60); }

    function showToast(msg) {
      const t = document.getElementById('t_toast');
      t.textContent = msg;
      t.classList.add('show');
      setTimeout(function () { t.classList.remove('show'); }, 2200);
    }

    function setStatus(msg) { document.getElementById('t_status').textContent = msg; }

    function init(token, profile, role) {
      idToken = token;
      document.getElementById('t_avatar').src = profile.pictureUrl || '';
      document.getElementById('t_displayName').textContent = 'Hi, ' + profile.displayName;
      document.getElementById('t_profileBar').style.display = 'flex';
      if (role && role.lineUserId) {
        document.getElementById('t_footer').textContent = 'LINE ID: ' + role.lineUserId;
      }
      loadData();
    }

    function loadData() {
      setStatus('Loading your schedule...');
      dates = buildDateList();
      const startIso = new Date(dates[0].getTime()).toISOString();
      const endD = new Date(dates[dates.length - 1].getTime());
      endD.setDate(endD.getDate() + 1);
      const endIso = endD.toISOString();

      google.script.run
        .withSuccessHandler(function (res) {
          if (!res || !res.ok) {
            setStatus('Failed to load. Please refresh the page and try again.');
            return;
          }
          selected = {};
          res.slots.forEach(function (s) {
            if (!selected[s.date]) selected[s.date] = new Set();
            selected[s.date].add(s.start + '-' + s.end);
          });
          busyByDate = {};
          res.busy.forEach(function (b) {
            const bs = new Date(b.start);
            const be = new Date(b.end);
            const dateKey = toDateStr(bs);
            if (!busyByDate[dateKey]) busyByDate[dateKey] = [];
            busyByDate[dateKey].push({
              startMin: bs.getHours() * 60 + bs.getMinutes(),
              endMin: be.getHours() * 60 + be.getMinutes(),
              isMine: b.isMine,
            });
          });

          document.getElementById('t_status').style.display = 'none';
          document.getElementById('t_app').style.display = 'block';
          document.getElementById('t_submitBar').style.display = 'flex';
          renderDateRow();
          selectDate(toDateStr(dates[0]));
        })
        .withFailureHandler(function (err) {
          setStatus('Failed to load: ' + err.message);
        })
        .getMyAvailability(idToken, startIso, endIso);
    }

    const weekNames = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];

    function renderDateRow() {
      const row = document.getElementById('t_dateRow');
      row.innerHTML = '';
      dates.forEach(function (d) {
        const key = toDateStr(d);
        const chip = document.createElement('div');
        chip.className = 'dateChip' + (key === activeDate ? ' active' : '');
        const count = selected[key] ? selected[key].size : 0;
        chip.innerHTML =
          '<span class="w">' + weekNames[d.getDay()] + '</span>' +
          '<span class="d">' + (d.getMonth() + 1) + '/' + d.getDate() + '</span>' +
          (count > 0 ? '<span class="count">' + count + ' selected</span>' : '');
        chip.addEventListener('click', function () { selectDate(key); });
        row.appendChild(chip);
      });
    }

    function selectDate(key) {
      activeDate = key;
      renderDateRow();
      const d = dates.find(function (dd) { return toDateStr(dd) === key; });
      document.getElementById('t_slotAreaTitle').textContent =
        'Available time slots for ' + weekNames[d.getDay()] + ', ' + (d.getMonth() + 1) + '/' + d.getDate();

      const grid = document.getElementById('t_slotGrid');
      grid.innerHTML = '';
      const slots = buildSlotsForDay();
      const busyList = busyByDate[key] || [];
      const mySelected = selected[key] || new Set();

      slots.forEach(function (s) {
        const label = minToHHMM(s.startMin) + '-' + minToHHMM(s.endMin);
        const btn = document.createElement('div');
        btn.className = 'slotBtn';

        const overlap = busyList.find(function (b) {
          return s.startMin < b.endMin && s.endMin > b.startMin;
        });

        if (overlap) {
          btn.classList.add('busy');
          if (overlap.isMine) {
            btn.classList.add('mine');
            btn.innerHTML = label + '<small>Booked</small>';
          } else {
            btn.innerHTML = label + '<small>Unavailable</small>';
          }
        } else {
          if (mySelected.has(label)) btn.classList.add('selected');
          btn.textContent = label;
          btn.addEventListener('click', function () {
            if (!selected[key]) selected[key] = new Set();
            if (selected[key].has(label)) {
              selected[key].delete(label);
              btn.classList.remove('selected');
            } else {
              selected[key].add(label);
              btn.classList.add('selected');
            }
            updateSummary();
            renderDateRow();
          });
        }
        grid.appendChild(btn);
      });

      updateSummary();
    }

    function updateSummary() {
      let total = 0;
      Object.keys(selected).forEach(function (k) { total += selected[k].size; });
      document.getElementById('t_summary').textContent =
        total > 0 ? (total + ' time slot' + (total === 1 ? '' : 's') + ' selected') : 'No time slots selected yet';
    }

    document.getElementById('t_submitBtn').addEventListener('click', function () {
      const btn = document.getElementById('t_submitBtn');
      btn.disabled = true;
      btn.textContent = 'Submitting...';

      const slots = [];
      Object.keys(selected).forEach(function (date) {
        selected[date].forEach(function (label) {
          const parts = label.split('-');
          slots.push({ date: date, start: parts[0], end: parts[1] });
        });
      });
      const clearDates = dates.map(toDateStr);

      google.script.run
        .withSuccessHandler(function (res) {
          btn.disabled = false;
          btn.textContent = 'Submit';
          if (res && res.ok) {
            showToast('Submitted! Thank you.');
          } else {
            showToast('Submission failed, please try again.');
          }
        })
        .withFailureHandler(function (err) {
          btn.disabled = false;
          btn.textContent = 'Submit';
          showToast('Submission failed: ' + err.message);
        })
        .saveAvailability(idToken, slots, clearDates);
    });

    return { init: init };
  })();
</script>

<script>
  // ==========================================================
  // AdminApp
  // ==========================================================
  var AdminApp = (function () {
    let idToken = null;
    let lastResult = null;
    let lastStartStr = null;
    let lastEndStr = null;
    let currentLang = 'zh';

    const LANG = {
      zh: {
        title: '老師排課管理後台',
        subtitle: '查看每位老師可上課時段,並比對 Google 行事曆上已排定的課程',
        startDate: '開始日期',
        endDate: '結束日期',
        refresh: '重新整理',
        lastUpdated: '更新時間: ',
        legendFree: '老師回報可上課',
        legendBusy: '行事曆上已排課(比對到該老師)',
        statusLoading: '載入中...',
        loadFailed: '讀取失敗: ',
        unknownError: '未知錯誤',
        teacherCol: '老師',
        noTeachersYet: '目前還沒有老師透過 LIFF 登入過',
        unmatchedTitle: function (n) { return '行事曆上有 ' + n + ' 筆事件沒有比對到任何老師'; },
        unmatchedDesc: '可能是活動標題/描述中沒有包含老師姓名。可到 Teachers 工作表調整每位老師的 MatchKeyword 欄位。',
        noTitlePlaceholder: '(無標題)',
        langBtn: 'EN',
        weekNames: ['日', '一', '二', '三', '四', '五', '六'],
        tabSchedule: '課表總覽',
        tabTeachers: '老師名單',
        colName: '姓名',
        colLineId: 'LINE ID',
        colKeyword: '比對關鍵字',
        colFirstSeen: '首次使用',
        colLastUpdated: '最近使用',
        copyBtn: '複製',
        copiedMsg: '已複製!',
        teachersRefresh: '重新整理',
        teachersCount: function (n) { return '共 ' + n + ' 位老師'; },
        noTeachersRoster: '目前還沒有老師打開過排課連結',
      },
      en: {
        title: 'Teacher Scheduling Admin',
        subtitle: "View every teacher's availability and cross-check it against your Google Calendar",
        startDate: 'Start date',
        endDate: 'End date',
        refresh: 'Refresh',
        lastUpdated: 'Last updated: ',
        legendFree: 'Reported available',
        legendBusy: 'Booked on calendar (matched to teacher)',
        statusLoading: 'Loading...',
        loadFailed: 'Failed to load: ',
        unknownError: 'Unknown error',
        teacherCol: 'Teacher',
        noTeachersYet: 'No teacher has logged in via LIFF yet',
        unmatchedTitle: function (n) { return n + ' calendar event(s) could not be matched to any teacher'; },
        unmatchedDesc: "The event title/description may not contain the teacher's name. You can adjust each teacher's MatchKeyword in the Teachers sheet.",
        noTitlePlaceholder: '(no title)',
        langBtn: '中文',
        weekNames: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'],
        tabSchedule: 'Schedule Overview',
        tabTeachers: 'Teacher Roster',
        colName: 'Name',
        colLineId: 'LINE ID',
        colKeyword: 'Match Keyword',
        colFirstSeen: 'First seen',
        colLastUpdated: 'Last active',
        copyBtn: 'Copy',
        copiedMsg: 'Copied!',
        teachersRefresh: 'Refresh',
        teachersCount: function (n) { return n + ' teacher(s)'; },
        noTeachersRoster: 'No teacher has opened the scheduling link yet',
      },
    };

    function t(key) { return LANG[currentLang][key]; }

    function pad(n) { return n < 10 ? '0' + n : '' + n; }
    function toDateStr(d) { return d.getFullYear() + '-' + pad(d.getMonth() + 1) + '-' + pad(d.getDate()); }
    function fromDateStr(s) {
      const parts = s.split('-');
      return new Date(parseInt(parts[0], 10), parseInt(parts[1], 10) - 1, parseInt(parts[2], 10));
    }

    function defaultRange() {
      const start = new Date(); start.setHours(0, 0, 0, 0);
      const end = new Date(start.getTime());
      end.setDate(end.getDate() + CONFIG.WEEKS_AHEAD * 7 - 1);
      return { start: start, end: end };
    }

    function initDates() {
      const r = defaultRange();
      document.getElementById('a_startDate').value = toDateStr(r.start);
      document.getElementById('a_endDate').value = toDateStr(r.end);
    }

    function dateRangeList(startStr, endStr) {
      const list = [];
      let cur = fromDateStr(startStr);
      const end = fromDateStr(endStr);
      while (cur <= end) {
        list.push(new Date(cur.getTime()));
        cur.setDate(cur.getDate() + 1);
      }
      return list;
    }

    function mergeRanges(items) {
      if (!items.length) return [];
      const sorted = items.slice().sort(function (a, b) { return a.start < b.start ? -1 : 1; });
      const out = [{ start: sorted[0].start, end: sorted[0].end }];
      for (let i = 1; i < sorted.length; i++) {
        const last = out[out.length - 1];
        if (sorted[i].start <= last.end) {
          if (sorted[i].end > last.end) last.end = sorted[i].end;
        } else {
          out.push({ start: sorted[i].start, end: sorted[i].end });
        }
      }
      return out;
    }

    function toHHMM(iso) {
      const d = new Date(iso);
      return pad(d.getHours()) + ':' + pad(d.getMinutes());
    }

    function escapeHtml(s) {
      return String(s).replace(/[&<>"']/g, function (c) {
        return { '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;' }[c];
      });
    }

    let currentView = 'schedule'; // 'schedule' | 'teachers'
    let lastTeachers = null;

    function applyStaticText() {
      document.getElementById('a_hTitle').textContent = t('title');
      document.getElementById('a_hSubtitle').textContent = t('subtitle');
      document.getElementById('a_langBtn').textContent = t('langBtn');
      document.getElementById('a_lblStart').textContent = t('startDate');
      document.getElementById('a_lblEnd').textContent = t('endDate');
      document.getElementById('a_refreshBtn').textContent = t('refresh');
      document.getElementById('a_legFree').textContent = t('legendFree');
      document.getElementById('a_legBusy').textContent = t('legendBusy');
      document.getElementById('a_tabSchedule').textContent = t('tabSchedule');
      document.getElementById('a_tabTeachers').textContent = t('tabTeachers');
      document.getElementById('a_teachersRefreshBtn').textContent = t('teachersRefresh');
    }

    document.getElementById('a_langBtn').addEventListener('click', function () {
      currentLang = currentLang === 'zh' ? 'en' : 'zh';
      applyStaticText();
      if (lastResult) render(lastResult, lastStartStr, lastEndStr);
      if (lastTeachers) renderTeachers(lastTeachers);
    });

    function showView(view) {
      currentView = view;
      document.getElementById('a_scheduleView').style.display = view === 'schedule' ? 'block' : 'none';
      document.getElementById('a_teachersView').style.display = view === 'teachers' ? 'block' : 'none';
      document.getElementById('a_tabSchedule').classList.toggle('active', view === 'schedule');
      document.getElementById('a_tabTeachers').classList.toggle('active', view === 'teachers');
      if (view === 'teachers' && !lastTeachers) loadTeachers();
    }

    document.getElementById('a_tabSchedule').addEventListener('click', function () { showView('schedule'); });
    document.getElementById('a_tabTeachers').addEventListener('click', function () { showView('teachers'); });
    document.getElementById('a_teachersRefreshBtn').addEventListener('click', loadTeachers);

    function copyToClipboard(text, btn) {
      const done = function () {
        const original = btn.textContent;
        btn.textContent = t('copiedMsg');
        setTimeout(function () { btn.textContent = original; }, 1500);
      };
      if (navigator.clipboard && navigator.clipboard.writeText) {
        navigator.clipboard.writeText(text).then(done).catch(function () { window.prompt('Copy:', text); });
      } else {
        window.prompt('Copy:', text);
      }
    }

    function loadTeachers() {
      document.getElementById('a_teachersTable').innerHTML = '';
      document.getElementById('a_teachersCount').textContent = t('statusLoading');
      google.script.run
        .withSuccessHandler(function (res) {
          if (!res || !res.ok) {
            document.getElementById('a_teachersCount').textContent = t('loadFailed') + (res ? res.error : t('unknownError'));
            return;
          }
          lastTeachers = res.teachers;
          renderTeachers(res.teachers);
        })
        .withFailureHandler(function (err) {
          document.getElementById('a_teachersCount').textContent = t('loadFailed') + err.message;
        })
        .getTeacherRoster(idToken);
    }

    function renderTeachers(teachers) {
      document.getElementById('a_teachersCount').textContent = t('teachersCount')(teachers.length);
      const table = document.getElementById('a_teachersTable');
      let html = '<tr><th>' + t('colName') + '</th><th>' + t('colLineId') + '</th><th>' +
        t('colKeyword') + '</th><th>' + t('colFirstSeen') + '</th><th>' + t('colLastUpdated') + '</th></tr>';
      if (teachers.length === 0) {
        html += '<tr><td colspan="5" class="empty">' + t('noTeachersRoster') + '</td></tr>';
      }
      table.innerHTML = html;
      teachers.forEach(function (tc) {
        const tr = document.createElement('tr');
        tr.innerHTML =
          '<td>' + escapeHtml(tc.name) + '</td>' +
          '<td style="font-family:monospace;">' + escapeHtml(tc.lineUserId) + '</td>' +
          '<td>' + escapeHtml(tc.matchKeyword) + '</td>' +
          '<td>' + (tc.firstSeen ? new Date(tc.firstSeen).toLocaleString() : '-') + '</td>' +
          '<td>' + (tc.lastUpdated ? new Date(tc.lastUpdated).toLocaleString() : '-') + '</td>';
        const idCell = tr.children[1];
        const copyBtn = document.createElement('button');
        copyBtn.className = 'copyIdBtn';
        copyBtn.textContent = t('copyBtn');
        copyBtn.addEventListener('click', function () { copyToClipboard(tc.lineUserId, copyBtn); });
        idCell.appendChild(copyBtn);
        table.appendChild(tr);
      });
    }

    function init(token) {
      idToken = token;
      applyStaticText();
      initDates();
      load();
    }

    function load() {
      document.getElementById('a_status').style.display = 'block';
      document.getElementById('a_status').textContent = t('statusLoading');
      document.getElementById('a_app').style.display = 'none';

      const startStr = document.getElementById('a_startDate').value;
      const endStr = document.getElementById('a_endDate').value;
      const startIso = fromDateStr(startStr).toISOString();
      const endExclusive = fromDateStr(endStr);
      endExclusive.setDate(endExclusive.getDate() + 1);
      const endIso = endExclusive.toISOString();

      google.script.run
        .withSuccessHandler(function (res) {
          if (!res || !res.ok) {
            document.getElementById('a_status').textContent = t('loadFailed') + (res ? res.error : t('unknownError'));
            return;
          }
          document.getElementById('a_status').style.display = 'none';
          document.getElementById('a_app').style.display = 'block';
          render(res, startStr, endStr);
        })
        .withFailureHandler(function (err) {
          document.getElementById('a_status').textContent = t('loadFailed') + err.message;
        })
        .getAdminOverview(idToken, startIso, endIso);
    }

    function render(res, startStr, endStr) {
      lastResult = res;
      lastStartStr = startStr;
      lastEndStr = endStr;

      const dates = dateRangeList(startStr, endStr);
      const weekNames = t('weekNames');

      const availByTeacherDate = {};
      res.availability.forEach(function (a) {
        availByTeacherDate[a.lineUserId] = availByTeacherDate[a.lineUserId] || {};
        availByTeacherDate[a.lineUserId][a.date] = availByTeacherDate[a.lineUserId][a.date] || [];
        availByTeacherDate[a.lineUserId][a.date].push({ start: a.start, end: a.end });
      });

      const busyByTeacherDate = {};
      const unmatched = [];
      res.busy.forEach(function (b) {
        const d = new Date(b.start);
        const dateKey = toDateStr(d);
        if (b.matchedTeacher) {
          busyByTeacherDate[b.matchedTeacher] = busyByTeacherDate[b.matchedTeacher] || {};
          busyByTeacherDate[b.matchedTeacher][dateKey] = busyByTeacherDate[b.matchedTeacher][dateKey] || [];
          busyByTeacherDate[b.matchedTeacher][dateKey].push({ start: toHHMM(b.start), end: toHHMM(b.end) });
        } else {
          unmatched.push({ date: dateKey, title: b.title, start: toHHMM(b.start), end: toHHMM(b.end) });
        }
      });

      const table = document.getElementById('a_overviewTable');
      let html = '<tr><th>' + t('teacherCol') + '</th>';
      dates.forEach(function (d) {
        html += '<th>' + (d.getMonth() + 1) + '/' + d.getDate() + ' (' + weekNames[d.getDay()] + ')</th>';
      });
      html += '</tr>';

      if (res.teachers.length === 0) {
        html += '<tr><td colspan="' + (dates.length + 1) + '" class="empty">' + t('noTeachersYet') + '</td></tr>';
      }

      res.teachers.forEach(function (tc) {
        html += '<tr><td class="teacherCell">' + escapeHtml(tc.name) + '</td>';
        dates.forEach(function (d) {
          const dateKey = toDateStr(d);
          const free = mergeRanges((availByTeacherDate[tc.lineUserId] || {})[dateKey] || []);
          const busy = mergeRanges((busyByTeacherDate[tc.lineUserId] || {})[dateKey] || []);
          let cell = '';
          free.forEach(function (r) { cell += '<span class="chip free">' + r.start + '-' + r.end + '</span>'; });
          busy.forEach(function (r) { cell += '<span class="chip busyMine">' + r.start + '-' + r.end + '</span>'; });
          if (!cell) cell = '<span class="empty">-</span>';
          html += '<td>' + cell + '</td>';
        });
        html += '</tr>';
      });

      table.innerHTML = html;
      document.getElementById('a_tableWrap').style.display = 'block';
      document.getElementById('a_lastUpdated').textContent = t('lastUpdated') + new Date().toLocaleString();

      const panel = document.getElementById('a_unmatchedPanel');
      if (unmatched.length) {
        let uHtml = '<h3>' + escapeHtml(t('unmatchedTitle')(unmatched.length)) + '</h3>' +
          '<p>' + escapeHtml(t('unmatchedDesc')) + '</p><ul>';
        unmatched.slice(0, 20).forEach(function (u) {
          uHtml += '<li>' + u.date + ' ' + u.start + '-' + u.end + ' “' +
            escapeHtml(u.title || t('noTitlePlaceholder')) + '”</li>';
        });
        uHtml += '</ul>';
        panel.innerHTML = uHtml;
        panel.style.display = 'block';
      } else {
        panel.style.display = 'none';
      }
    }

    document.getElementById('a_refreshBtn').addEventListener('click', load);

    return { init: init };
  })();
</script>

</body>
</html>
