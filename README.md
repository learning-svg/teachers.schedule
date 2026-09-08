<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
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
  #teacherRoot { display: none; padding-bottom: 40px; }

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

  #t_app { padding: 16px; display: none; }

  #t_dayTabs { display: flex; gap: 6px; margin-bottom: 14px; flex-wrap: wrap; }
  .dayTab {
    flex: 1 1 auto;
    min-width: 40px;
    padding: 8px 4px;
    border-radius: 8px;
    border: 1px solid #e0e0e0;
    background: #fff;
    font-size: 12px;
    text-align: center;
    cursor: pointer;
  }
  .dayTab .cnt { display: block; font-size: 10px; color: #06913c; margin-top: 2px; }
  .dayTab.active { background: #222; border-color: #222; color: #fff; }
  .dayTab.active .cnt { color: #9be7c4; }
  .dayTab.hasSlots:not(.active) { border-color: #06C755; }

  #t_legend {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
    padding: 0 0 12px;
    font-size: 11px;
    color: #666;
  }
  #t_legend span { display: inline-flex; align-items: center; gap: 4px; }
  .dot { width: 10px; height: 10px; border-radius: 3px; display: inline-block; }

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
    line-height: 1.3;
  }
  .slotBtn.open { background: #06C755; border-color: #06C755; color: #fff; font-weight: 700; }
  .slotBtn.booked { background: #2e7dfa; border-color: #2e7dfa; color: #fff; font-weight: 700; cursor: default; }
  .slotBtn small { display: block; font-size: 10px; margin-top: 2px; }

  #t_footer {
    text-align: center;
    padding: 20px 16px 4px;
    font-size: 10px;
    color: #bbb;
  }

  #t_saveBar {
    margin-top: 18px;
    display: none;
  }
  #t_saveBtn {
    width: 100%;
    background: #06C755;
    color: #fff;
    border: none;
    border-radius: 10px;
    padding: 13px 20px;
    font-size: 15px;
    font-weight: 700;
    cursor: pointer;
  }
  #t_saveBtn:disabled { background: #bbb; }

  #t_toast {
    position: fixed;
    left: 50%; bottom: 24px;
    transform: translateX(-50%);
    background: #333;
    color: #fff;
    padding: 10px 16px;
    border-radius: 8px;
    font-size: 13px;
    opacity: 0;
    transition: opacity .25s;
    pointer-events: none;
    max-width: 90%;
    text-align: center;
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

  .adminContainer { padding: 16px 20px 60px; max-width: 900px; margin: 0 auto; }

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

  .copyIdBtn {
    background: #f5f6f7;
    border: 1px solid #ddd;
    border-radius: 6px;
    padding: 2px 8px;
    font-size: 11px;
    cursor: pointer;
    margin-left: 6px;
  }

  .adminToolbar {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    align-items: center;
    margin-bottom: 14px;
  }

  /* ---- Open Slots: folder tabs ---- */
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
  .folderPanel .panelTitle { font-size: 15px; font-weight: 700; margin: 0 0 4px; }
  .folderPanel .panelSub { font-size: 12px; color: #888; margin: 0 0 14px; }

  .openDayRow { display: flex; align-items: flex-start; gap: 10px; padding: 8px 0; border-top: 1px solid #f2f2f2; }
  .openDayRow:first-of-type { border-top: none; }
  .openDayRow .dName { flex: 0 0 44px; font-size: 12px; font-weight: 700; color: #555; padding-top: 4px; }
  .openDayRow .dChips { flex: 1; display: flex; flex-wrap: wrap; gap: 4px; }

  .chip { display: inline-block; padding: 3px 8px; border-radius: 6px; font-size: 11px; white-space: nowrap; margin: 2px 2px 0 0; }
  .chip.open { background: #e6f9ee; color: #06913c; border: 1px solid #b8ecd0; }

  .emptyState { text-align: center; color: #999; font-size: 12px; padding: 24px 10px; }

  /* ---- Teacher roster table ---- */
  .tableWrap { overflow-x: auto; border-radius: 10px; }
  #a_teachersTable { border-collapse: collapse; width: 100%; background: #fff; border-radius: 10px; overflow: hidden; }
  #a_teachersTable th, #a_teachersTable td { border: 1px solid #eee; padding: 8px; vertical-align: top; font-size: 12px; }
  #a_teachersTable th { background: #fafafa; text-align: left; }

  #a_status { text-align: center; color: #888; padding: 30px; font-size: 13px; }
  .empty { color: #ccc; font-size: 11px; }
</style>
</head>
<body>

<div id="bootStatus">Connecting to LINE...</div>

<!-- ===================== Teacher view ===================== -->
<div id="teacherRoot">
  <header>
    <h1>My Weekly Schedule</h1>
    <p>These are the times you teach every week. This repeats automatically — you won't need to fill it in again unless your regular schedule changes.</p>
  </header>

  <div id="t_profileBar" style="display:none;">
    <img id="t_avatar" src="" alt="">
    <span id="t_displayName"></span>
  </div>

  <div id="t_status">Loading your schedule...</div>

  <div id="t_app">
    <div id="t_dayTabs"></div>
    <div id="t_legend">
      <span><i class="dot" style="background:#2e7dfa;"></i>Booked (student matched from calendar)</span>
      <span><i class="dot" style="background:#06C755;"></i>My usual time (open)</span>
      <span><i class="dot" style="background:#fff;border:1px solid #e0e0e0;"></i>Not scheduled</span>
    </div>
    <div class="slotGrid" id="t_slotGrid"></div>

    <div id="t_saveBar">
      <button id="t_saveBtn">Save Weekly Schedule</button>
    </div>

    <div id="t_footer"></div>
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
    <div class="adminTabs">
      <button class="tabBtn active" id="a_tabOpenSlots"></button>
      <button class="tabBtn" id="a_tabTeachers"></button>
    </div>

    <!-- ---- Open Slots (folder tabs per teacher) ---- -->
    <div id="a_openSlotsView">
      <div class="adminToolbar">
        <button class="btn" id="a_openRefreshBtn"></button>
        <span id="a_openLastUpdated" style="font-size:11px;color:#999;"></span>
      </div>
      <div id="a_openStatus" style="text-align:center;color:#888;padding:24px;font-size:13px;"></div>
      <div id="a_openApp" style="display:none;">
        <div class="folderTabs" id="a_folderTabs"></div>
        <div class="folderPanel" id="a_folderPanel"></div>
      </div>
    </div>

    <!-- ---- Teacher Roster ---- -->
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

<script>
  // ==========================================================
  // >>>>>>>>>> 這裡是你唯一需要修改的地方 <<<<<<<<<<
  // ==========================================================
  // LIFF_ID:LINE Developers Console 裡 LIFF App 的 ID
  // GAS_EXEC_URL:Google Apps Script 部署成「網路應用程式」後拿到的網址
  //              (格式類似 https://script.google.com/macros/s/xxxx/exec)
  const LIFF_ID = 'YOUR_LIFF_ID_HERE';
  const GAS_EXEC_URL = '[https://script.google.com/macros/s/AKfycbxPw648Qh3CnNIGvkWF__I-A-d3Bci550hxUkV7bVRrrSatOX5hrtzUzsNM8QoesGpS/exec';
  // ==========================================================

  // 排課參數的預設值(登入成功後會自動被 Code.gs 的 CONFIG 覆蓋,
  // 這裡只是在還沒登入完成前，畫面需要用到時的暫時預設值）
  const CONFIG = { SLOT_START_HOUR: 9, SLOT_END_HOUR: 21, SLOT_MINUTES: 30 };

  /**
   * 呼叫 Google Apps Script 後端。
   *
   * 重要:body 用 JSON.stringify 送出,但「不要」自己加
   * `headers: { 'Content-Type': 'application/json' }`。
   * 瀏覽器在沒有自訂 headers 時,fetch 的 POST 預設是 text/plain,
   * 這樣才會被視為「簡單請求」,不會觸發 CORS 的 OPTIONS 預檢 ——
   * 而 Apps Script 的網路應用程式並不支援回應預檢請求。
   * Code.gs 那邊會自己用 JSON.parse(e.postData.contents) 解析內容,
   * 所以不需要正確的 Content-Type 也沒關係。
   */
  function callApi(action, params) {
    const payload = Object.assign({ action: action }, params || {});
    return fetch(GAS_EXEC_URL, {
      method: 'POST',
      body: JSON.stringify(payload),
    }).then(function (res) {
      if (!res.ok) throw new Error('HTTP ' + res.status);
      return res.json();
    });
  }

  // ==========================================================
  // Shared bootstrap: one LIFF login, then route by role
  // ==========================================================
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
      return callApi('getMyRole', { idToken: idToken });
    }).then(function (role) {
      if (!role) return; // redirecting to login
      document.getElementById('bootStatus').style.display = 'none';
      if (role.config) Object.assign(CONFIG, role.config);

      if (role.ok && role.isAdmin) {
        document.getElementById('adminRoot').style.display = 'block';
        AdminApp.init(idToken);
      } else if (role.ok) {
        document.getElementById('teacherRoot').style.display = 'block';
        TeacherApp.init(idToken, profile, role);
      } else {
        document.getElementById('bootStatus').style.display = 'block';
        document.getElementById('bootStatus').textContent = 'Failed to check permissions: ' + (role.message || role.error || 'unknown error');
      }
    }).catch(function (err) {
      document.getElementById('bootStatus').style.display = 'block';
      document.getElementById('bootStatus').textContent = 'LINE login failed: ' + err;
    });
  }

  boot();
</script>

<script>
  // ==========================================================
  // TeacherApp — "My Weekly Schedule"
  // ==========================================================
  var TeacherApp = (function () {
    const DAY_KEYS = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
    let idToken = null;
    let template = []; // [{day, start, end, student}]
    let activeDay = 'Mon';
    let dirty = false;

    function pad(n) { return n < 10 ? '0' + n : '' + n; }
    function hhmm(m) { return pad(Math.floor(m / 60)) + ':' + pad(m % 60); }
    function slotLabel(m) { return hhmm(m) + '-' + hhmm(m + CONFIG.SLOT_MINUTES); }

    function slotList() {
      const out = [];
      for (let m = CONFIG.SLOT_START_HOUR * 60; m < CONFIG.SLOT_END_HOUR * 60; m += CONFIG.SLOT_MINUTES) {
        out.push(m);
      }
      return out;
    }

    function findSlot(day, startHHMM) {
      return template.find(function (s) { return s.day === day && s.start === startHHMM; });
    }

    function countForDay(day) {
      return template.filter(function (s) { return s.day === day; }).length;
    }

    function showToast(msg) {
      const t = document.getElementById('t_toast');
      t.textContent = msg;
      t.classList.add('show');
      setTimeout(function () { t.classList.remove('show'); }, 2600);
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
      document.getElementById('t_app').style.display = 'none';

      callApi('getMyWeeklyTemplate', { idToken: idToken })
        .then(function (res) {
          if (!res || !res.ok) {
            setStatus('Failed to load. Please refresh the page and try again.');
            return;
          }
          template = res.template || [];
          dirty = false;
          document.getElementById('t_status').style.display = 'none';
          document.getElementById('t_app').style.display = 'block';
          renderDayTabs();
          renderSlotGrid();
        })
        .catch(function (err) {
          setStatus('Failed to load: ' + err.message);
        });
    }

    function renderDayTabs() {
      const wrap = document.getElementById('t_dayTabs');
      wrap.innerHTML = '';
      DAY_KEYS.forEach(function (k) {
        const cnt = countForDay(k);
        const tab = document.createElement('div');
        tab.className = 'dayTab' + (k === activeDay ? ' active' : '') + (cnt > 0 ? ' hasSlots' : '');
        tab.innerHTML = k + (cnt > 0 ? '<span class="cnt">' + cnt + '</span>' : '');
        tab.addEventListener('click', function () { activeDay = k; renderDayTabs(); renderSlotGrid(); });
        wrap.appendChild(tab);
      });
    }

    function renderSlotGrid() {
      const grid = document.getElementById('t_slotGrid');
      grid.innerHTML = '';
      slotList().forEach(function (m) {
        const startStr = hhmm(m);
        const endStr = hhmm(m + CONFIG.SLOT_MINUTES);
        const slot = findSlot(activeDay, startStr);
        const btn = document.createElement('div');
        btn.className = 'slotBtn';

        if (slot && slot.student) {
          btn.classList.add('booked');
          btn.innerHTML = slotLabel(m) + '<small>with ' + escapeHtml(slot.student) + '</small>';
          btn.addEventListener('click', function () {
            showToast('Matched from your Google Calendar booking. To change this, edit the class directly in Calendar.');
          });
        } else if (slot) {
          btn.classList.add('open');
          btn.innerHTML = slotLabel(m) + '<small>Open</small>';
          btn.addEventListener('click', function () {
            template = template.filter(function (s) { return s !== slot; });
            dirty = true;
            renderDayTabs();
            renderSlotGrid();
          });
        } else {
          btn.textContent = slotLabel(m);
          btn.addEventListener('click', function () {
            template.push({ day: activeDay, start: startStr, end: endStr, student: null });
            dirty = true;
            renderDayTabs();
            renderSlotGrid();
          });
        }
        grid.appendChild(btn);
      });
      document.getElementById('t_saveBar').style.display = dirty ? 'block' : 'none';
    }

    function escapeHtml(s) {
      return String(s).replace(/[&<>"']/g, function (c) {
        return { '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;' }[c];
      });
    }

    document.getElementById('t_saveBtn').addEventListener('click', function () {
      const btn = document.getElementById('t_saveBtn');
      btn.disabled = true;
      btn.textContent = 'Saving...';

      const slots = template.map(function (s) { return { day: s.day, start: s.start, end: s.end }; });

      callApi('saveWeeklyTemplate', { idToken: idToken, slots: slots })
        .then(function (res) {
          btn.disabled = false;
          btn.textContent = 'Save Weekly Schedule';
          if (res && res.ok) {
            showToast('Saved! This schedule now applies to every week.');
            loadData(); // 重新載入,順便拿到最新的行事曆比對結果
          } else {
            showToast('Save failed, please try again.');
          }
        })
        .catch(function (err) {
          btn.disabled = false;
          btn.textContent = 'Save Weekly Schedule';
          showToast('Save failed: ' + err.message);
        });
    });

    return { init: init };
  })();
</script>

<script>
  // ==========================================================
  // AdminApp — Open Slots (folder tabs) + Teacher Roster
  // ==========================================================
  var AdminApp = (function () {
    const DAY_KEYS = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'];
    let idToken = null;
    let currentLang = 'zh';
    let currentView = 'openSlots'; // 'openSlots' | 'teachers'

    let lastTeachersOverview = null; // getAdminWeeklyOverview 的 teachers[]
    let activeTeacherId = null;
    let lastRoster = null; // getTeacherRoster 的 teachers[]

    const LANG = {
      zh: {
        title: '老師排課管理後台',
        subtitle: '瀏覽每位老師的固定課表,快速找到還能安排新學生的空堂',
        langBtn: 'EN',
        tabOpenSlots: '老師空堂',
        tabTeachers: '老師名單',
        refresh: '重新整理',
        lastUpdated: '更新時間: ',
        statusLoading: '載入中...',
        loadFailed: '讀取失敗: ',
        unknownError: '未知錯誤',
        dayShort: { Mon: '一', Tue: '二', Wed: '三', Thu: '四', Fri: '五', Sat: '六', Sun: '日' },
        panelSubtitle: function (n) {
          return n > 0
            ? ('目前有 ' + n + ' 個開放時段可以安排新學生')
            : '目前所有固定時段都已經有學生,沒有空堂可以安排新學生。';
        },
        folderCnt: function (n) { return n + ' 開放'; },
        noTeachersOpen: '目前還沒有老師設定固定課表',
        colName: '姓名',
        colLineId: 'LINE ID',
        colKeyword: '比對關鍵字',
        colFirstSeen: '首次使用',
        colLastUpdated: '最近使用',
        copyBtn: '複製',
        copiedMsg: '已複製!',
        teachersCount: function (n) { return '共 ' + n + ' 位老師'; },
        noTeachersRoster: '目前還沒有老師打開過排課連結',
      },
      en: {
        title: 'Teacher Scheduling Admin',
        subtitle: "Browse each teacher's weekly schedule and quickly find open slots for a new student",
        langBtn: '中文',
        tabOpenSlots: 'Open Slots',
        tabTeachers: 'Teacher Roster',
        refresh: 'Refresh',
        lastUpdated: 'Last updated: ',
        statusLoading: 'Loading...',
        loadFailed: 'Failed to load: ',
        unknownError: 'Unknown error',
        dayShort: { Mon: 'Mon', Tue: 'Tue', Wed: 'Wed', Thu: 'Thu', Fri: 'Fri', Sat: 'Sat', Sun: 'Sun' },
        panelSubtitle: function (n) {
          return n > 0
            ? (n + ' open slot' + (n === 1 ? '' : 's') + ' available to book')
            : "All of this teacher's regular time slots are currently booked.";
        },
        folderCnt: function (n) { return n + ' open'; },
        noTeachersOpen: 'No teacher has set up a weekly schedule yet',
        colName: 'Name',
        colLineId: 'LINE ID',
        colKeyword: 'Match Keyword',
        colFirstSeen: 'First seen',
        colLastUpdated: 'Last active',
        copyBtn: 'Copy',
        copiedMsg: 'Copied!',
        teachersCount: function (n) { return n + ' teacher(s)'; },
        noTeachersRoster: 'No teacher has opened the scheduling link yet',
      },
    };

    function t(key) { return LANG[currentLang][key]; }

    function escapeHtml(s) {
      return String(s).replace(/[&<>"']/g, function (c) {
        return { '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#39;' }[c];
      });
    }

    function applyStaticText() {
      document.getElementById('a_hTitle').textContent = t('title');
      document.getElementById('a_hSubtitle').textContent = t('subtitle');
      document.getElementById('a_langBtn').textContent = t('langBtn');
      document.getElementById('a_tabOpenSlots').textContent = t('tabOpenSlots');
      document.getElementById('a_tabTeachers').textContent = t('tabTeachers');
      document.getElementById('a_openRefreshBtn').textContent = t('refresh');
      document.getElementById('a_teachersRefreshBtn').textContent = t('refresh');
    }

    document.getElementById('a_langBtn').addEventListener('click', function () {
      currentLang = currentLang === 'zh' ? 'en' : 'zh';
      applyStaticText();
      if (lastTeachersOverview) renderFolderTabs();
      if (lastTeachersOverview) renderFolderPanel();
      if (lastRoster) renderTeachers(lastRoster);
    });

    function showView(view) {
      currentView = view;
      document.getElementById('a_openSlotsView').style.display = view === 'openSlots' ? 'block' : 'none';
      document.getElementById('a_teachersView').style.display = view === 'teachers' ? 'block' : 'none';
      document.getElementById('a_tabOpenSlots').classList.toggle('active', view === 'openSlots');
      document.getElementById('a_tabTeachers').classList.toggle('active', view === 'teachers');
      if (view === 'teachers' && !lastRoster) loadTeachers();
    }
    document.getElementById('a_tabOpenSlots').addEventListener('click', function () { showView('openSlots'); });
    document.getElementById('a_tabTeachers').addEventListener('click', function () { showView('teachers'); });
    document.getElementById('a_openRefreshBtn').addEventListener('click', loadOpenSlots);
    document.getElementById('a_teachersRefreshBtn').addEventListener('click', loadTeachers);

    // ---------------- Open Slots (folder tabs per teacher) ----------------
    function loadOpenSlots() {
      document.getElementById('a_openStatus').style.display = 'block';
      document.getElementById('a_openStatus').textContent = t('statusLoading');
      document.getElementById('a_openApp').style.display = 'none';

      callApi('getAdminWeeklyOverview', { idToken: idToken })
        .then(function (res) {
          if (!res || !res.ok) {
            document.getElementById('a_openStatus').textContent = t('loadFailed') + (res ? res.error : t('unknownError'));
            return;
          }
          lastTeachersOverview = res.teachers || [];
          if (!activeTeacherId && lastTeachersOverview.length) activeTeacherId = lastTeachersOverview[0].lineUserId;
          document.getElementById('a_openStatus').style.display = 'none';
          document.getElementById('a_openApp').style.display = 'block';
          document.getElementById('a_openLastUpdated').textContent = t('lastUpdated') + new Date().toLocaleString();
          renderFolderTabs();
          renderFolderPanel();
        })
        .catch(function (err) {
          document.getElementById('a_openStatus').textContent = t('loadFailed') + err.message;
        });
    }

    function renderFolderTabs() {
      const wrap = document.getElementById('a_folderTabs');
      wrap.innerHTML = '';
      if (!lastTeachersOverview || !lastTeachersOverview.length) {
        wrap.innerHTML = '<div class="empty" style="padding:10px 4px;">' + t('noTeachersOpen') + '</div>';
        return;
      }
      lastTeachersOverview.forEach(function (tc) {
        const tab = document.createElement('div');
        tab.className = 'folderTab' + (tc.lineUserId === activeTeacherId ? ' active' : '');
        tab.innerHTML = escapeHtml(tc.name) + '<span class="cnt">' + t('folderCnt')(tc.openCount) + '</span>';
        tab.addEventListener('click', function () {
          activeTeacherId = tc.lineUserId;
          renderFolderTabs();
          renderFolderPanel();
        });
        wrap.appendChild(tab);
      });
    }

    function renderFolderPanel() {
      const panel = document.getElementById('a_folderPanel');
      const tc = (lastTeachersOverview || []).find(function (x) { return x.lineUserId === activeTeacherId; });
      if (!tc) { panel.innerHTML = ''; return; }

      let html = '<div class="panelTitle">' + escapeHtml(tc.name) + '</div>';
      html += '<div class="panelSub">' + escapeHtml(t('panelSubtitle')(tc.openCount)) + '</div>';

      if (tc.openCount > 0) {
        DAY_KEYS.forEach(function (k) {
          const openSlots = tc.template
            .filter(function (s) { return s.day === k && !s.student; })
            .sort(function (a, b) { return a.start < b.start ? -1 : 1; });
          if (openSlots.length === 0) return;
          html += '<div class="openDayRow"><div class="dName">' + t('dayShort')[k] + '</div><div class="dChips">';
          openSlots.forEach(function (s) {
            html += '<span class="chip open">' + s.start + '-' + s.end + '</span>';
          });
          html += '</div></div>';
        });
      }

      panel.innerHTML = html;
    }

    // ---------------- Teacher Roster ----------------
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
      callApi('getTeacherRoster', { idToken: idToken })
        .then(function (res) {
          if (!res || !res.ok) {
            document.getElementById('a_teachersCount').textContent = t('loadFailed') + (res ? res.error : t('unknownError'));
            return;
          }
          lastRoster = res.teachers;
          renderTeachers(res.teachers);
        })
        .catch(function (err) {
          document.getElementById('a_teachersCount').textContent = t('loadFailed') + err.message;
        });
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
      showView('openSlots');
      loadOpenSlots();
    }

    return { init: init };
  })();
</script>

</body>
</html>
