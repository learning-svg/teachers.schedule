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
  #teacherRoot { display: none; padding-bottom: 110px; }

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

  #t_emailCard {
    background: #fff;
    border: 1px solid #e0e0e0;
    border-radius: 10px;
    padding: 14px;
    margin-bottom: 16px;
  }
  #t_emailCard.needsEmail { border-color: #f0b429; background: #fffbf0; }
  #t_emailCard h3 { margin: 0 0 4px; font-size: 13px; }
  #t_emailCard p { margin: 0 0 10px; font-size: 11px; color: #888; line-height: 1.5; }
  #t_emailRow { display: flex; gap: 8px; }
  #t_emailInput {
    flex: 1;
    min-width: 0;
    padding: 9px 10px;
    border: 1px solid #ddd;
    border-radius: 8px;
    font-size: 14px;
  }
  #t_emailSaveBtn {
    flex: 0 0 auto;
    background: #222;
    color: #fff;
    border: none;
    border-radius: 8px;
    padding: 9px 16px;
    font-size: 13px;
    font-weight: 700;
    cursor: pointer;
  }
  #t_emailSaveBtn:disabled { background: #bbb; }

  /* Learning Portal 入口:放在 email 欄位下面、星期選單上面 */
  #t_portalBtn {
    display: flex;
    align-items: center;
    gap: 10px;
    width: 100%;
    background: #2e7dfa;
    color: #fff;
    border: none;
    border-radius: 10px;
    padding: 13px 16px;
    margin-bottom: 16px;
    font-family: inherit;
    text-align: left;
    cursor: pointer;
    text-decoration: none;
  }
  #t_portalBtn .icon { font-size: 20px; flex: 0 0 auto; }
  #t_portalBtn .txt { flex: 1; min-width: 0; }
  #t_portalBtn .txt b { display: block; font-size: 14px; font-weight: 700; }
  #t_portalBtn .txt small { display: block; font-size: 11px; opacity: .9; margin-top: 2px; }
  #t_portalBtn .arrow { flex: 0 0 auto; font-size: 16px; opacity: .8; }

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

  /* 儲存列固定在畫面最下方,老師不用捲到底才找得到按鈕 */
  #t_saveBar {
    position: fixed;
    left: 0; right: 0; bottom: 0;
    background: #fff;
    border-top: 1px solid #e0e0e0;
    padding: 10px 16px;
    padding-bottom: calc(10px + env(safe-area-inset-bottom, 0px));
    display: none;
    z-index: 20;
    box-shadow: 0 -2px 8px rgba(0,0,0,.06);
  }
  #t_saveHint { font-size: 11px; color: #888; margin-bottom: 6px; text-align: center; }
  #t_saveHint.unsaved { color: #d17a00; font-weight: 700; }
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
  #t_saveBtn.clean { background: #9aa0a6; }
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
  .openDayRow .dName { flex: 0 0 52px; font-size: 12px; font-weight: 700; color: #555; padding-top: 4px; }
  .openDayRow .dChips { flex: 1; display: flex; flex-wrap: wrap; gap: 4px; }

  .chip { display: inline-block; padding: 3px 8px; border-radius: 6px; font-size: 11px; white-space: nowrap; margin: 2px 2px 0 0; }
  .chip.open { background: #e6f9ee; color: #06913c; border: 1px solid #b8ecd0; }

  .emptyState { text-align: center; color: #999; font-size: 12px; padding: 24px 10px; }

  /* ---- 時段找老師 ---- */
  .dayTabsRow { display: flex; gap: 6px; margin-bottom: 14px; flex-wrap: wrap; }
  .plainPanel { background: #fff; border: 1px solid #e0e0e0; border-radius: 10px; padding: 16px; }
  .timeRow { display: flex; align-items: flex-start; gap: 12px; padding: 9px 0; border-top: 1px solid #f2f2f2; }
  .timeRow:first-of-type { border-top: none; }
  .timeRow .tSlot {
    flex: 0 0 112px;
    font-size: 13px;
    font-weight: 700;
    padding-top: 3px;
    font-variant-numeric: tabular-nums;
  }
  .timeRow .tNames { flex: 1; display: flex; flex-wrap: wrap; gap: 4px; }
  .chip.teacher { background: #eef4ff; color: #2554d1; border: 1px solid #c3d3fb; }

  .warnBox {
    background: #fffbf0;
    border: 1px solid #f0d98c;
    border-radius: 8px;
    padding: 8px 12px;
    font-size: 11px;
    color: #6b5900;
    line-height: 1.5;
    margin-bottom: 12px;
  }

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
    <div id="t_emailCard">
      <h3>Your email address</h3>
      <p>Use the email address where you receive the calendar invitations for your classes. This is how the system knows which classes on the calendar are yours.</p>
      <div id="t_emailRow">
        <input id="t_emailInput" type="email" inputmode="email" autocapitalize="off" autocorrect="off" spellcheck="false" placeholder="you@example.com">
        <button id="t_emailSaveBtn">Save</button>
      </div>
    </div>

    <a id="t_portalBtn" href="#" rel="noopener">
      <span class="icon">📚</span>
      <span class="txt">
        <b>Learning Portal</b>
        <small>Student homework, feedback &amp; teaching materials</small>
      </span>
      <span class="arrow">›</span>
    </a>

    <div id="t_dayTabs"></div>
    <div id="t_legend">
      <span><i class="dot" style="background:#2e7dfa;"></i>Booked (student matched from calendar)</span>
      <span><i class="dot" style="background:#06C755;"></i>My usual time (open)</span>
      <span><i class="dot" style="background:#fff;border:1px solid #e0e0e0;"></i>Not scheduled</span>
    </div>
    <div class="slotGrid" id="t_slotGrid"></div>

    <div id="t_saveBar">
      <div id="t_saveHint"></div>
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
      <button class="tabBtn" id="a_tabByTime"></button>
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

    <!-- ---- 時段找老師 ---- -->
    <div id="a_byTimeView" style="display:none;">
      <div class="adminToolbar">
        <button class="btn" id="a_timeRefreshBtn"></button>
        <span id="a_timeUpdated" style="font-size:11px;color:#999;"></span>
      </div>
      <div id="a_timeStatus" style="text-align:center;color:#888;padding:24px;font-size:13px;"></div>
      <div id="a_timeApp" style="display:none;">
        <div class="dayTabsRow" id="a_timeDayTabs"></div>
        <div class="plainPanel">
          <div class="panelTitle" id="a_timeTitle" style="font-size:15px;font-weight:700;margin-bottom:4px;"></div>
          <div class="panelSub" id="a_timeSub" style="font-size:12px;color:#888;margin-bottom:14px;"></div>
          <div id="a_timeList"></div>
        </div>
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
  const LIFF_ID = '2009789905-1PJRkuCz';
  const GAS_EXEC_URL = 'https://script.google.com/macros/s/AKfycbxPw648Qh3CnNIGvkWF__I-A-d3Bci550hxUkV7bVRrrSatOX5hrtzUzsNM8QoesGpS/exec';
  // 老師頁面上「Learning Portal」按鈕要連到的網址(學生作業、回饋、教材)
  const LEARNING_PORTAL_URL = 'https://liff.line.me/2008845693-L2SUJz8X';
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
  /**
   * LINE 的登入憑證(ID Token)大約一小時就過期,而且 LIFF 不會自動換新的:
   * 使用者看起來還是「已登入」,但送到後端的憑證已經失效,後端就會回報驗證失敗。
   * 下面這幾個函式負責偵測這種情況,並自動重新登入一次拿到新的憑證。
   */
  function isTokenExpired(res) {
    if (!res || res.ok) return false;
    const msg = String(res.message || '') + ' ' + String(res.error || '');
    return msg.indexOf('expired') !== -1 || msg.indexOf('IdToken') !== -1;
  }

  // 記錄上次自動重新登入的時間,避免萬一一直失敗時陷入無限跳轉
  function relogRecently() {
    try {
      const t = parseInt(sessionStorage.getItem('reloginAt') || '0', 10);
      return !!t && (Date.now() - t < 30000);
    } catch (err) { return false; }
  }

  function forceRelogin() {
    try { sessionStorage.setItem('reloginAt', String(Date.now())); } catch (err) {}
    try { liff.logout(); } catch (err) {}
    try {
      liff.login({ redirectUri: window.location.href.split('#')[0] });
    } catch (err) {
      window.location.reload();
    }
  }

  function callApi(action, params) {
    const payload = Object.assign({ action: action }, params || {});
    return fetch(GAS_EXEC_URL, {
      method: 'POST',
      body: JSON.stringify(payload),
    }).then(function (res) {
      if (!res.ok) throw new Error('HTTP ' + res.status);
      return res.json();
    }).then(function (json) {
      // 憑證過期就自動重新登入(頁面會跳轉,拿到新憑證後回到原本畫面)
      if (isTokenExpired(json) && !relogRecently()) forceRelogin();
      return json;
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
      // 先自己檢查憑證的到期時間,快過期就直接重新登入,不用等後端報錯
      const decoded = liff.getDecodedIDToken();
      if (!decoded || !decoded.exp || (decoded.exp * 1000) <= Date.now() + 60000) {
        if (!relogRecently()) {
          document.getElementById('bootStatus').textContent = 'Session expired, signing you in again...';
          forceRelogin();
          return;
        }
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
        document.getElementById('bootStatus').textContent = isTokenExpired(role)
          ? 'Your login has expired. Please close this page and open the link again.'
          : ('Failed to check permissions: ' + (role.message || role.error || 'unknown error'));
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
      document.getElementById('t_saveBar').style.display = 'none';

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
          renderEmail(res.email || '');
          renderDayTabs();
          renderSlotGrid();
        })
        .catch(function (err) {
          setStatus('Failed to load: ' + err.message);
        });
    }

    document.getElementById('t_portalBtn').addEventListener('click', function (e) {
      e.preventDefault();
      // 在 LINE 裡用 LIFF 開新視窗,關掉之後還能回到這個排課頁面
      try {
        if (typeof liff !== 'undefined' && liff.openWindow) {
          liff.openWindow({ url: LEARNING_PORTAL_URL, external: false });
          return;
        }
      } catch (err) { /* 不在 LINE 環境就用下面的方式開 */ }
      window.open(LEARNING_PORTAL_URL, '_blank');
    });

    function renderEmail(email) {
      document.getElementById('t_emailInput').value = email;
      // 還沒填 email 時,把這張卡片標成黃色提醒老師先填
      document.getElementById('t_emailCard').classList.toggle('needsEmail', !email);
    }

    document.getElementById('t_emailSaveBtn').addEventListener('click', function () {
      const btn = document.getElementById('t_emailSaveBtn');
      const email = document.getElementById('t_emailInput').value.trim();
      if (!email) {
        showToast('Please enter your email address first.');
        return;
      }
      btn.disabled = true;
      btn.textContent = 'Saving...';

      callApi('saveMyEmail', { idToken: idToken, email: email })
        .then(function (res) {
          btn.disabled = false;
          btn.textContent = 'Save';
          if (res && res.ok) {
            showToast('Email saved. Your booked classes will now show up automatically.');
            loadData(); // 重新比對行事曆,馬上看到已預約的學生
          } else if (res && res.error === 'INVALID_EMAIL') {
            showToast('That does not look like a valid email address.');
          } else {
            showToast('Save failed, please try again.');
          }
        })
        .catch(function (err) {
          btn.disabled = false;
          btn.textContent = 'Save';
          showToast('Save failed: ' + err.message);
        });
    });

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
      updateSaveBar();
    }

    /**
     * 儲存列永遠留在畫面上,只是依「有沒有未存檔的變更」改變外觀:
     * 有變更 -> 綠色按鈕 + 橘色提示;沒變更 -> 灰色按鈕 + 已儲存提示。
     * (按鈕不會鎖住,老師隨時可以再按一次存檔,不會覺得系統壞掉)
     */
    function updateSaveBar() {
      const bar = document.getElementById('t_saveBar');
      const btn = document.getElementById('t_saveBtn');
      const hint = document.getElementById('t_saveHint');
      bar.style.display = 'block';
      if (dirty) {
        btn.classList.remove('clean');
        hint.className = 'unsaved';
        hint.textContent = 'You have unsaved changes';
      } else {
        btn.classList.add('clean');
        hint.className = '';
        hint.textContent = 'All changes saved';
      }
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
        tabByTime: '時段找老師',
        tabTeachers: '老師名單',
        refresh: '重新整理',
        lastUpdated: '更新時間: ',
        statusLoading: '載入中...',
        loadFailed: '讀取失敗: ',
        unknownError: '未知錯誤',
        dayShort: { Mon: '週一', Tue: '週二', Wed: '週三', Thu: '週四', Fri: '週五', Sat: '週六', Sun: '週日' },
        panelSubtitle: function (n) {
          return n > 0
            ? ('目前有 ' + n + ' 個開放時段可以安排新學生')
            : '目前所有固定時段都已經有學生,沒有空堂可以安排新學生。';
        },
        folderCnt: function (n) { return n + ' 開放'; },
        noTeachersOpen: '目前還沒有老師設定固定課表',
        timeTitle: function (d) { return d + ' 有空堂的時段'; },
        timeSub: function (n) {
          return n > 0
            ? ('這一天有 ' + n + ' 個時段可以安排新學生,點下面的時間看有哪些老師有空')
            : '這一天目前所有老師的固定時段都已經有學生了';
        },
        timeDayCnt: function (n) { return n + ' 段'; },
        noOpenThisDay: '這一天目前沒有可以安排新學生的時段',
        noEmailWarning: '這位老師還沒填 email,目前是用「名字比對行事曆」,建議請他打開連結填一下 email 會更準確。',
        colName: '姓名',
        colLineId: 'LINE ID',
        colEmail: 'Email',
        emailMissing: '(未填)',
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
        tabByTime: 'Find by Time',
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
        timeTitle: function (d) { return 'Open time slots on ' + d; },
        timeSub: function (n) {
          return n > 0
            ? (n + ' time slot' + (n === 1 ? '' : 's') + ' available to book a new student')
            : 'Every regular slot on this day is already booked';
        },
        timeDayCnt: function (n) { return '' + n; },
        noOpenThisDay: 'No open time slots on this day',
        noEmailWarning: "This teacher hasn't entered an email yet, so their classes are matched by name. Ask them to open the link and fill it in for accurate matching.",
        colName: 'Name',
        colLineId: 'LINE ID',
        colEmail: 'Email',
        emailMissing: '(not set)',
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
      document.getElementById('a_tabByTime').textContent = t('tabByTime');
      document.getElementById('a_timeRefreshBtn').textContent = t('refresh');
      document.getElementById('a_tabTeachers').textContent = t('tabTeachers');
      document.getElementById('a_openRefreshBtn').textContent = t('refresh');
      document.getElementById('a_teachersRefreshBtn').textContent = t('refresh');
    }

    document.getElementById('a_langBtn').addEventListener('click', function () {
      currentLang = currentLang === 'zh' ? 'en' : 'zh';
      applyStaticText();
      if (lastTeachersOverview) renderFolderTabs();
      if (lastTeachersOverview) renderFolderPanel();
      if (lastTeachersOverview) renderByTime();
      if (lastRoster) renderTeachers(lastRoster);
    });

    function showView(view) {
      currentView = view;
      document.getElementById('a_openSlotsView').style.display = view === 'openSlots' ? 'block' : 'none';
      document.getElementById('a_byTimeView').style.display = view === 'byTime' ? 'block' : 'none';
      document.getElementById('a_teachersView').style.display = view === 'teachers' ? 'block' : 'none';
      document.getElementById('a_tabOpenSlots').classList.toggle('active', view === 'openSlots');
      document.getElementById('a_tabByTime').classList.toggle('active', view === 'byTime');
      document.getElementById('a_tabTeachers').classList.toggle('active', view === 'teachers');
      if (view === 'teachers' && !lastRoster) loadTeachers();
      if (view === 'byTime') {
        // 兩個分頁用的是同一份資料,已經載過就直接畫,不用再打一次 API
        if (lastTeachersOverview) renderByTime();
        else loadOpenSlots();
      }
    }
    document.getElementById('a_tabOpenSlots').addEventListener('click', function () { showView('openSlots'); });
    document.getElementById('a_tabByTime').addEventListener('click', function () { showView('byTime'); });
    document.getElementById('a_tabTeachers').addEventListener('click', function () { showView('teachers'); });
    document.getElementById('a_timeRefreshBtn').addEventListener('click', function () { loadOpenSlots(true); });
    document.getElementById('a_openRefreshBtn').addEventListener('click', function () { loadOpenSlots(true); });
    document.getElementById('a_teachersRefreshBtn').addEventListener('click', loadTeachers);

    // ---------------- Open Slots (folder tabs per teacher) ----------------
    function loadOpenSlots(force) {
      document.getElementById('a_openStatus').style.display = 'block';
      document.getElementById('a_openStatus').textContent = t('statusLoading');
      document.getElementById('a_openApp').style.display = 'none';

      callApi('getAdminWeeklyOverview', { idToken: idToken, refresh: !!force })
        .then(function (res) {
          if (!res || !res.ok) {
            document.getElementById('a_openStatus').textContent = t('loadFailed') + (res ? res.error : t('unknownError'));
            return;
          }
          lastTeachersOverview = res.teachers || [];
          if (!activeTeacherId && lastTeachersOverview.length) activeTeacherId = lastTeachersOverview[0].lineUserId;
          document.getElementById('a_openStatus').style.display = 'none';
          document.getElementById('a_openApp').style.display = 'block';
          // 顯示資料實際產生的時間(可能來自 2 分鐘內的快取),而不是畫面繪製時間
          const generated = res.generatedAt ? new Date(res.generatedAt) : new Date();
          document.getElementById('a_openLastUpdated').textContent = t('lastUpdated') + generated.toLocaleString();
          renderFolderTabs();
          renderFolderPanel();
          renderByTime();
        })
        .catch(function (err) {
          document.getElementById('a_openStatus').textContent = t('loadFailed') + err.message;
          document.getElementById('a_timeStatus').textContent = t('loadFailed') + err.message;
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
      if (!tc.hasEmail) {
        html += '<div class="warnBox">' + escapeHtml(t('noEmailWarning')) + '</div>';
      }

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

    // ---------------- 時段找老師(把同一份資料改用「時間」的角度切) ----------------
    let timeActiveDay = 'Mon';

    function slotStartList() {
      const out = [];
      for (let m = CONFIG.SLOT_START_HOUR * 60; m < CONFIG.SLOT_END_HOUR * 60; m += CONFIG.SLOT_MINUTES) {
        out.push(m);
      }
      return out;
    }
    function padNum(n) { return n < 10 ? '0' + n : '' + n; }
    function minToHhmm(m) { return padNum(Math.floor(m / 60)) + ':' + padNum(m % 60); }

    // 這個星期幾、這個時間點,有哪些老師是空的
    function openTeachersAt(day, startStr) {
      return (lastTeachersOverview || []).filter(function (tc) {
        return (tc.template || []).some(function (s) {
          return s.day === day && s.start === startStr && !s.student;
        });
      });
    }

    function openSlotCountForDay(day) {
      let n = 0;
      slotStartList().forEach(function (m) {
        if (openTeachersAt(day, minToHhmm(m)).length > 0) n++;
      });
      return n;
    }

    function renderByTime() {
      if (!lastTeachersOverview) return;
      document.getElementById('a_timeStatus').style.display = 'none';
      document.getElementById('a_timeApp').style.display = 'block';
      document.getElementById('a_timeUpdated').textContent =
        document.getElementById('a_openLastUpdated').textContent;

      // 星期標籤(顯示每天有幾個時段還排得進新學生)
      const tabs = document.getElementById('a_timeDayTabs');
      tabs.innerHTML = '';
      DAY_KEYS.forEach(function (k) {
        const cnt = openSlotCountForDay(k);
        const tab = document.createElement('div');
        tab.className = 'dayTab' + (k === timeActiveDay ? ' active' : '') + (cnt > 0 ? ' hasSlots' : '');
        tab.innerHTML = t('dayShort')[k] + (cnt > 0 ? '<span class="cnt">' + t('timeDayCnt')(cnt) + '</span>' : '');
        tab.addEventListener('click', function () { timeActiveDay = k; renderByTime(); });
        tabs.appendChild(tab);
      });

      // 時段清單:只列出「至少有一位老師有空」的時間
      const list = document.getElementById('a_timeList');
      list.innerHTML = '';
      let shown = 0;
      slotStartList().forEach(function (m) {
        const startStr = minToHhmm(m);
        const endStr = minToHhmm(m + CONFIG.SLOT_MINUTES);
        const teachers = openTeachersAt(timeActiveDay, startStr);
        if (!teachers.length) return;
        shown++;

        const row = document.createElement('div');
        row.className = 'timeRow';
        let names = '';
        teachers.forEach(function (tc) {
          names += '<span class="chip teacher">' + escapeHtml(tc.name) + '</span>';
        });
        row.innerHTML = '<div class="tSlot">' + startStr + '-' + endStr + '</div>' +
          '<div class="tNames">' + names + '</div>';
        list.appendChild(row);
      });

      document.getElementById('a_timeTitle').textContent = t('timeTitle')(t('dayShort')[timeActiveDay]);
      document.getElementById('a_timeSub').textContent = t('timeSub')(shown);
      if (!shown) {
        list.innerHTML = '<div class="emptyState">' + t('noOpenThisDay') + '</div>';
      }
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
        t('colEmail') + '</th><th>' +
        t('colKeyword') + '</th><th>' + t('colFirstSeen') + '</th><th>' + t('colLastUpdated') + '</th></tr>';
      if (teachers.length === 0) {
        html += '<tr><td colspan="6" class="empty">' + t('noTeachersRoster') + '</td></tr>';
      }
      table.innerHTML = html;
      teachers.forEach(function (tc) {
        const tr = document.createElement('tr');
        const emailCell = tc.email
          ? escapeHtml(tc.email)
          : '<span style="color:#d17a00;">' + t('emailMissing') + '</span>';
        tr.innerHTML =
          '<td>' + escapeHtml(tc.name) + '</td>' +
          '<td style="font-family:monospace;">' + escapeHtml(tc.lineUserId) + '</td>' +
          '<td>' + emailCell + '</td>' +
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
