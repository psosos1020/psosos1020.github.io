<!doctype html>
<html lang="ko">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>게이들 도감</title>
<style>
  :root { --bg:#0f172a; --card:#0b1220; --muted:#94a3b8; --accent:#06b6d4; --ok:#10b981; --danger:#ef4444; --glass: rgba(255,255,255,0.03); }
  html,body{height:100%;margin:0;font-family:system-ui,-apple-system,"Segoe UI",Roboto,"Noto Sans KR","Apple SD Gothic Neo","Malgun Gothic";background:linear-gradient(180deg,#071024,#041026);color:#e6eef6}
  .container{max-width:1100px;margin:28px auto;padding:18px}
  h1{margin:0 0 12px;font-size:20px}
  .layout{display:grid;grid-template-columns:1fr 380px;gap:18px}
  .card{background:var(--card);padding:14px;border-radius:12px;box-shadow:0 8px 30px rgba(2,6,23,0.6)}
  label{display:block;margin:10px 0 6px;color:var(--muted);font-size:13px}
  input[type=text], textarea, select {width:100%;padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:inherit}
  textarea{min-height:90px;resize:vertical}
  button{padding:8px 12px;border-radius:8px;border:0;background:var(--accent);color:#021124;font-weight:700;cursor:pointer}
  .muted{color:var(--muted);font-size:13px}
  .small{font-size:13px}
  .preview{border-radius:8px;overflow:hidden;background:var(--glass);padding:8px}
  .entry-list{display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:12px;margin-top:12px}
  .entry{background:linear-gradient(180deg, rgba(255,255,255,0.02), transparent);padding:10px;border-radius:10px}
  .entry img{width:100%;height:140px;object-fit:cover;border-radius:6px}
  .tags{margin-top:8px;display:flex;flex-wrap:wrap;gap:6px}
  .tag{background:rgba(255,255,255,0.04);padding:4px 8px;border-radius:6px;font-size:12px;color:var(--muted)}
  .meta{margin-top:8px;color:var(--muted);font-size:13px}
  .btn-ghost{background:transparent;border:1px solid rgba(255,255,255,0.04);color:inherit;padding:6px 8px;border-radius:8px;cursor:pointer}
  .admin-link{display:flex;gap:8px;align-items:center;justify-content:space-between}
  .hidden{display:none}
  .hr{height:1px;background:rgba(255,255,255,0.03);margin:12px 0;border-radius:1px}
  .center{display:flex;align-items:center;gap:8px}
  .danger{color:var(--danger);font-weight:700}
  .ok{color:var(--ok);font-weight:700}
  .controls{display:flex;gap:8px;flex-wrap:wrap;margin-top:10px}
  .small-muted{font-size:12px;color:var(--muted)}
  .search{display:flex;gap:8px;align-items:center;margin-bottom:8px}
  .search input{flex:1}
  footer{margin-top:18px;color:var(--muted);font-size:13px;text-align:center}
  /* admin panel */
  .admin-panel{position:fixed;top:18px;right:18px;width:420px;max-height:88vh;overflow:auto;padding:12px;z-index:60}
  .list-item{display:flex;gap:10px;align-items:flex-start;padding:8px;border-radius:8px;background:rgba(255,255,255,0.02);margin-bottom:8px}
  .list-item img{width:72px;height:56px;object-fit:cover;border-radius:6px}
  .list-item .meta{flex:1}
  .actions{display:flex;gap:6px}
  .pill{padding:6px 8px;border-radius:8px;border:0;background:rgba(255,255,255,0.03);cursor:pointer}
</style>
</head>
<body>
  <div class="container">
    <header class="card" style="margin-bottom:12px;display:flex;justify-content:space-between;align-items:center">
      <div>
        <h1>게이 도감</h1>
        <div class="muted small">게이를 등록할수 있는 사이트</div>
      </div>
      <div class="admin-link">
        <div class="small-muted">관리자: <span id="adminState" class="ok">로그아웃</span></div>
        <div>
          <button id="openAdminBtn" class="btn-ghost">관리자 열기</button>
        </div>
      </div>
    </header>

    <div class="layout">
      <!-- left: public site: submission form + entries -->
      <div>
        <div class="card" id="submitCard">
          <h2>새 게이 만들기</h2>
          <label for="title">이름</label>
          <input id="title" type="text" placeholder="항목 이름" />
          <label for="desc">설명</label>
          <textarea id="desc" placeholder="항목 설명 (간단히)"></textarea>
          <label>속성 추가 (키:값)</label>
          <div style="display:flex;gap:8px">
            <input id="statKey" type="text" placeholder="키 (예: 게이력)" />
            <input id="statVal" type="text" placeholder="값 (예: 50)" />
            <button id="addStatBtn">추가</button>
          </div>
          <div id="statsContainer" class="small-muted" style="margin-top:8px"></div>

          <label for="tags">태그 (쉼표로 구분)</label>
          <input id="tags" type="text" placeholder="예: 어패류,류광원" />

          <label for="img">이미지 (선택)</label>
          <input id="img" type="file" accept="image/*" />
          <div class="preview" style="margin-top:8px">
            <div class="muted small">미리보기</div>
            <img id="imgPreview" src="" alt="" style="width:100%;height:200px;object-fit:contain;border-radius:8px;display:none;margin-top:8px" />
          </div>

          <div class="controls">
            <button id="submitBtn">제출</button>
            <button id="clearForm" class="btn-ghost">폼 초기화</button>
            <div class="small-muted">제출된 항목은 승인 후 공개됩니다.</div>
          </div>

          <div id="submitMsg" class="small-muted" style="margin-top:10px"></div>
        </div>

        <div class="card" style="margin-top:12px">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <h2>도감 항목</h2>
            <div style="display:flex;gap:8px;align-items:center">
              <div class="small-muted">총 항목: <span id="entriesCount">0</span></div>
            </div>
          </div>

          <div class="hr"></div>

          <div class="search">
            <input id="searchInput" type="text" placeholder="검색 (이름/태그)" />
            <select id="filterTag">
              <option value="">모든 태그</option>
            </select>
            <button id="clearFilter" class="btn-ghost">초기화</button>
          </div>

          <div id="entries" class="entry-list"></div>
        </div>
      </div>

      <!-- right: info / about / quick controls -->
      <aside>
        <div class="card">
          <h3>운영 정보</h3>
          <div class="muted small">초기화 될수도 있는데 그건 내잘못이 아니라 깃헙잘못임</div>
          <div class="hr"></div>
          <div>
            <strong>로컬 저장 키</strong>
            <div class="small-muted">제출: <code>catalog_submissions_v1</code></div>
            <div class="small-muted">승인 항목: <code>catalog_entries_v1</code></div>
          </div>
          <div class="hr"></div>
          <div>
            <strong>관리자 설정</strong>
            <div class="small-muted">관리자 비밀번호 해시: 코드 상단 <code>qlalfqjsgh</code> 값 사용</div>
            <div style="margin-top:8px"><button id="howToSet" class="btn-ghost">설정 방법 보기</button></div>
          </div>
        </div>

        <div class="card" style="margin-top:12px">
          <h3>제출 현황</h3>
          <div id="pendingCount" class="muted small">대기중: 0</div>
          <div style="margin-top:10px">
            <button id="exportData" class="btn-ghost">데이터 내보내기(JSON)</button>
            <button id="importData" class="btn-ghost">데이터 불러오기(JSON)</button>
          </div>
        </div>
      </aside>
    </div>

    <footer>
      내부 테스트용 도감 시스템 — 관리자 승인이 있은 항목만 공개됩니다.
    </footer>
  </div>

  <!-- 관리자 패널 (오버레이) -->
  <div class="admin-panel card hidden" id="adminPanel">
    <div style="display:flex;justify-content:space-between;align-items:center">
      <h3>관리자 패널</h3>
      <div>
        <button id="closeAdmin" class="btn-ghost">닫기</button>
      </div>
    </div>

    <div id="adminLoginSection" style="margin-top:10px">
      <label for="adminPw">관리자 비밀번호</label>
      <input id="adminPw" type="password" />
      <div style="display:flex;gap:8px;margin-top:8px">
        <button id="adminLoginBtn">로그인</button>
        <button id="genHashBtn" class="btn-ghost">해시 생성</button>
      </div>
      <div id="adminMsg" class="small-muted" style="margin-top:8px"></div>
      <div class="hr" style="margin-top:12px"></div>
    </div>

    <!-- 로그인 후 보이는 내용 -->
    <div id="adminWorkspace" class="hidden">
      <div style="display:flex;justify-content:space-between;align-items:center">
        <div class="small-muted">로그인: <span id="whoIsAdmin" class="ok">관리자</span></div>
        <div style="display:flex;gap:8px">
          <button id="logoutAdmin" class="btn-ghost">로그아웃</button>
          <button id="purgeRejected" class="btn-ghost">반려 항목 일괄삭제</button>
        </div>
      </div>

      <div class="hr"></div>

      <h4>승인 대기 목록</h4>
      <div id="submissionList" style="margin-top:8px"></div>

      <div class="hr"></div>

      <h4>승인된 항목 (간단 편집)</h4>
      <div id="approvedList" style="margin-top:8px"></div>
    </div>
  </div>

<script>
/* ========= 설정 영역 =========
 - SALT: 공개되어도 무방한 소금값
 - STORED_HASH: (비밀번호 + SALT) SHA-256 헥스. generateHash(plain)로 생성.
 - 필요시 이 값만 바꿔 사용.
*/
const SALT = 'site-salt-v1';
const STORED_HASH = 'REPLACE_WITH_HASH'; // <--- 이 값을 generateHash로 생성한 해시로 바꿀 것

// localStorage 키
const SUB_KEY = 'catalog_submissions_v1';
const ENTRY_KEY = 'catalog_entries_v1';
const ADMIN_TOKEN_KEY = 'catalog_admin_token_v1';

// 최대 제출 제목 길이 등 간단한 제한
const MAX_TITLE = 80;
const MAX_DESC = 2000;

/* 유틸: SHA-256 헥스 */
async function sha256Hex(message) {
  const enc = new TextEncoder();
  const data = enc.encode(message);
  const hashBuffer = await crypto.subtle.digest('SHA-256', data);
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  return hashArray.map(b => b.toString(16).padStart(2,'0')).join('');
}
async function generateHash(plain) {
  const h = await sha256Hex(plain + SALT);
  console.log('생성된 해시:', h);
  alert('콘솔에 해시가 출력됩니다. 콘솔을 확인하세요.');
  return h;
}
window.generateHash = generateHash;

/* 저장/로딩 유틸 */
function loadJSON(key){ try{ const raw = localStorage.getItem(key); return raw ? JSON.parse(raw) : []; } catch(e){ return []; } }
function saveJSON(key, v){ localStorage.setItem(key, JSON.stringify(v)); }

/* 데이터 구조
 submission = {
   id: 's_...', title, desc, tags:[], stats:{k:v}, imageDataURL, user: {name?}, createdAt, status: 'pending'|'rejected'|'approved', adminComment
 }
 entry = {
   id: 'e_...', title, desc, tags, stats, imageDataURL, approvedAt, versions:[]
 }
*/

function uid(prefix='id'){ return prefix + '_' + Math.random().toString(36).slice(2,10); }

/* DOM 참조 */
const titleEl = document.getElementById('title');
const descEl = document.getElementById('desc');
const statKeyEl = document.getElementById('statKey');
const statValEl = document.getElementById('statVal');
const addStatBtn = document.getElementById('addStatBtn');
const statsContainer = document.getElementById('statsContainer');
const tagsEl = document.getElementById('tags');
const imgInput = document.getElementById('img');
const imgPreview = document.getElementById('imgPreview');
const submitBtn = document.getElementById('submitBtn');
const clearFormBtn = document.getElementById('clearForm');
const submitMsg = document.getElementById('submitMsg');

const entriesContainer = document.getElementById('entries');
const entriesCount = document.getElementById('entriesCount');
const pendingCount = document.getElementById('pendingCount');
const searchInput = document.getElementById('searchInput');
const filterTag = document.getElementById('filterTag');
const clearFilter = document.getElementById('clearFilter');

const openAdminBtn = document.getElementById('openAdminBtn');
const adminPanel = document.getElementById('adminPanel');
const closeAdmin = document.getElementById('closeAdmin');
const adminLoginBtn = document.getElementById('adminLoginBtn');
const adminPw = document.getElementById('adminPw');
const adminMsg = document.getElementById('adminMsg');
const adminWorkspace = document.getElementById('adminWorkspace');
const adminLoginSection = document.getElementById('adminLoginSection');
const whoIsAdmin = document.getElementById('whoIsAdmin');
const logoutAdmin = document.getElementById('logoutAdmin');
const submissionList = document.getElementById('submissionList');
const approvedList = document.getElementById('approvedList');
const adminState = document.getElementById('adminState');
const genHashBtn = document.getElementById('genHashBtn');
const howToSet = document.getElementById('howToSet');
const exportData = document.getElementById('exportData');
const importData = document.getElementById('importData');
const clearForm = document.getElementById('clearForm');
const openAdminBtnTop = openAdminBtn;
const purgeRejected = document.getElementById('purgeRejected');

/* 상태 */
let currentStats = {}; // 폼 내 속성
let cachedEntries = loadJSON(ENTRY_KEY);
let cachedSubs = loadJSON(SUB_KEY);

/* 이미지 미리보기 */
imgInput.addEventListener('change', async (e) => {
  const f = e.target.files && e.target.files[0];
  if (!f) { imgPreview.style.display='none'; imgPreview.src=''; return; }
  const reader = new FileReader();
  reader.onload = () => {
    imgPreview.src = reader.result;
    imgPreview.style.display = 'block';
  };
  reader.readAsDataURL(f);
});

/* 속성 추가 */
addStatBtn.addEventListener('click', () => {
  const k = (statKeyEl.value || '').trim();
  const v = (statValEl.value || '').trim();
  if (!k) return alert('키를 입력하세요.');
  currentStats[k] = v;
  renderStats();
  statKeyEl.value = ''; statValEl.value = '';
});
function renderStats(){
  const ks = Object.keys(currentStats);
  if (ks.length === 0) { statsContainer.textContent = '속성 없음'; return; }
  statsContainer.innerHTML = ks.map(k => `<div style="display:flex;gap:8px;align-items:center;margin-bottom:6px">
    <strong style="width:100px">${k}</strong><div class="small-muted">${currentStats[k]}</div>
    <button class="btn-ghost" data-rem="${k}" style="margin-left:auto">삭제</button>
  </div>`).join('');
  // 삭제 버튼 이벤트 delegation
  Array.from(statsContainer.querySelectorAll('[data-rem]')).forEach(btn=>{
    btn.onclick = () => { delete currentStats[btn.dataset.rem]; renderStats(); }
  });
}

/* 제출 처리 */
submitBtn.addEventListener('click', async () => {
  const title = (titleEl.value || '').trim();
  const desc = (descEl.value || '').trim();
  const tags = (tagsEl.value || '').split(',').map(s=>s.trim()).filter(Boolean);
  if (!title) return alert('이름을 입력하십시오.');
  if (title.length > MAX_TITLE) return alert('이름이 너무 깁니다.');
  if (desc.length > MAX_DESC) return alert('설명이 너무 깁니다.');

  // 이미지 읽기 (있으면 dataURL)
  let imageData = '';
  if (imgInput.files && imgInput.files[0]) {
    imageData = await new Promise((res, rej) => {
      const fr = new FileReader();
      fr.onload = () => res(fr.result);
      fr.onerror = rej;
      fr.readAsDataURL(imgInput.files[0]);
    });
  }

  const sub = {
    id: uid('s'),
    title, desc, tags, stats: {...currentStats}, imageDataURL: imageData,
    user: { anonymous: true }, createdAt: Date.now(), status: 'pending', adminComment: ''
  };

  cachedSubs.unshift(sub);
  saveJSON(SUB_KEY, cachedSubs);
  submitMsg.textContent = '제출 완료 — 관리자 승인 대기';
  clearFormData();
  renderAll();
});

/* 폼 초기화 */
function clearFormData(){
  titleEl.value=''; descEl.value=''; tagsEl.value=''; imgInput.value=''; imgPreview.src=''; imgPreview.style.display='none';
  currentStats = {}; renderStats();
}

/* 렌더링: 승인된 항목 목록 */
function renderEntries(filter='') {
  entriesContainer.innerHTML = '';
  const q = (searchInput.value || '').trim().toLowerCase();
  const tagFilter = (filterTag.value || '').trim();
  let items = cachedEntries.slice().reverse(); // 최신순
  if (q) items = items.filter(it => (it.title||'').toLowerCase().includes(q) || (it.tags||[]).some(t=>t.toLowerCase().includes(q)) || (it.desc||'').toLowerCase().includes(q));
  if (tagFilter) items = items.filter(it => (it.tags||[]).includes(tagFilter));
  entriesCount.textContent = items.length;
  // 태그 필터 업데이트
  updateFilterTags();

  if (items.length === 0) { entriesContainer.innerHTML = '<div class="muted small">표시할 항목이 없습니다.</div>'; return; }

  for (const it of items) {
    const div = document.createElement('div'); div.className = 'entry';
    const imgHtml = it.imageDataURL ? `<img src="${it.imageDataURL}" alt="img" />` : `<div style="width:100%;height:140px;border-radius:6px;background:rgba(255,255,255,0.02);display:flex;align-items:center;justify-content:center;color:var(--muted)">이미지 없음</div>`;
    div.innerHTML = `
      ${imgHtml}
      <h3 style="margin:8px 0 4px;font-size:16px">${escapeHtml(it.title)}</h3>
      <div class="meta">${escapeHtml(sliceText(it.desc, 140))}</div>
      <div class="tags">${(it.tags||[]).map(t=>`<div class="tag">${escapeHtml(t)}</div>`).join('')}</div>
      <div class="meta" style="margin-top:8px">${renderStatsInline(it.stats)}</div>
      <div style="display:flex;gap:8px;margin-top:8px"><button class="btn-ghost" data-id="${it.id}" onclick="viewDetail(this.dataset.id)">상세</button></div>
    `;
    entriesContainer.appendChild(div);
  }
}

/* 렌더링: 제출 대기 목록(관리자) */
function renderSubmissions() {
  submissionList.innerHTML = '';
  const pending = cachedSubs.filter(s => s.status === 'pending');
  pendingCount.textContent = pending.length;
  if (pending.length === 0) submissionList.innerHTML = '<div class="muted small">대기중인 제출이 없습니다.</div>';
  for (const s of pending) {
    const el = document.createElement('div'); el.className='list-item';
    el.innerHTML = `<img src="${s.imageDataURL || ''}" onerror="this.style.display='none'" /><div class="meta">
      <div style="display:flex;justify-content:space-between;align-items:center"><strong>${escapeHtml(s.title)}</strong><div class="small-muted">${new Date(s.createdAt).toLocaleString()}</div></div>
      <div class="small-muted" style="margin-top:6px">${escapeHtml(sliceText(s.desc,120))}</div>
      <div style="margin-top:8px" class="muted small">${Object.keys(s.stats||{}).length?renderStatsInline(s.stats):'속성 없음'}</div>
      <div class="actions" style="margin-top:8px">
        <button class="pill" data-id="${s.id}" data-act="approve">승인</button>
        <button class="pill" data-id="${s.id}" data-act="reject">반려</button>
        <button class="pill" data-id="${s.id}" data-act="view">보기</button>
      </div>
    </div>`;
    submissionList.appendChild(el);
  }
  // delegate actions
  submissionList.querySelectorAll('[data-act]').forEach(btn=>{
    btn.onclick = () => {
      const id = btn.dataset.id; const act = btn.dataset.act;
      const target = cachedSubs.find(x=>x.id===id);
      if (!target) return alert('항목을 찾을 수 없음');
      if (act === 'approve') { doApprove(target); }
      if (act === 'reject') { doReject(target); }
      if (act === 'view') { alert(`${target.title}\n\n${target.desc}`); }
    };
  });
  renderApprovedList();
}

/* 승인 로직: submission -> entries */
function doApprove(sub) {
  // 이미 승인된 유사 항목 체크는 하지 않음(단순 구현). 변환 후 entries에 추가
  const entry = {
    id: uid('e'),
    title: sub.title,
    desc: sub.desc,
    tags: sub.tags || [],
    stats: sub.stats || {},
    imageDataURL: sub.imageDataURL || '',
    approvedAt: Date.now(),
    versions: [{fromSubmissionId: sub.id, at: Date.now()}]
  };
  cachedEntries.push(entry);
  // 제출 상태 변경
  const idx = cachedSubs.findIndex(x=>x.id===sub.id);
  if (idx !== -1) cachedSubs.splice(idx,1);
  saveJSON(ENTRY_KEY, cachedEntries);
  saveJSON(SUB_KEY, cachedSubs);
  renderAll();
  alert('승인 처리 완료');
}

/* 반려 처리 */
function doReject(sub) {
  const idx = cachedSubs.findIndex(x=>x.id===sub.id);
  if (idx === -1) return;
  // 간단히 상태를 rejected로 옮기거나 삭제(여기서는 상태 표시)
  cachedSubs[idx].status = 'rejected';
  cachedSubs[idx].adminComment = '반려됨';
  saveJSON(SUB_KEY, cachedSubs);
  renderAll();
  alert('반려 처리됨');
}

/* 승인된 항목 편집 / 삭제 */
function renderApprovedList() {
  approvedList.innerHTML = '';
  const items = cachedEntries.slice().reverse();
  if (items.length === 0) approvedList.innerHTML = '<div class="muted small">승인된 항목이 없습니다.</div>';
  for (const it of items) {
    const div = document.createElement('div'); div.className='list-item';
    div.innerHTML = `<img src="${it.imageDataURL||''}" onerror="this.style.display='none'"/><div class="meta"><strong>${escapeHtml(it.title)}</strong><div class="small-muted" style="margin-top:6px">${escapeHtml(sliceText(it.desc,80))}</div><div style="margin-top:8px" class="actions">
      <button class="pill" data-id="${it.id}" data-act="edit">편집</button>
      <button class="pill" data-id="${it.id}" data-act="delete">삭제</button>
      <button class="pill" data-id="${it.id}" data-act="view">보기</button>
    </div></div>`;
    approvedList.appendChild(div);
  }
  approvedList.querySelectorAll('[data-act]').forEach(b=>{
    b.onclick = () => {
      const id = b.dataset.id; const act = b.dataset.act;
      const idx = cachedEntries.findIndex(x=>x.id===id);
      if (idx === -1) return alert('항목을 찾을 수 없음');
      if (act === 'delete') {
        if (!confirm('정말 삭제하시겠습니까?')) return;
        cachedEntries.splice(idx,1);
        saveJSON(ENTRY_KEY, cachedEntries); renderAll(); alert('삭제됨');
      }
      if (act === 'view') { const it = cachedEntries[idx]; alert(`${it.title}\n\n${it.desc}`); }
      if (act === 'edit') {
        const it = cachedEntries[idx];
        const newTitle = prompt('제목:', it.title); if (newTitle==null) return;
        const newDesc = prompt('설명:', it.desc); if (newDesc==null) return;
        it.title = newTitle; it.desc = newDesc;
        it.versions.push({editedAt:Date.now()});
        saveJSON(ENTRY_KEY, cachedEntries); renderAll(); alert('수정 저장됨');
      }
    };
  });
}

/* 보조: 텍스트 자르기, escape */
function sliceText(s,n){ if(!s) return ''; return s.length>n? s.slice(0,n-1)+'…': s; }
function escapeHtml(s){ if(!s) return ''; return String(s).replace(/[&<>"']/g, c=>({ '&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;' }[c])); }
function renderStatsInline(obj){ if(!obj) return ''; return Object.entries(obj).map(([k,v])=>`${escapeHtml(k)}: ${escapeHtml(v)}`).join(' · '); }

/* 검색/필터 이벤트 */
searchInput.addEventListener('input', ()=>renderEntries());
clearFilter.addEventListener('click', ()=>{ searchInput.value=''; filterTag.value=''; renderEntries(); });

function updateFilterTags(){
  // 모든 태그 집합 수집
  const tags = new Set();
  cachedEntries.forEach(e => (e.tags||[]).forEach(t => tags.add(t)));
  // 재구성
  const prev = filterTag.value;
  filterTag.innerHTML = `<option value="">모든 태그</option>` + Array.from(tags).sort().map(t=>`<option value="${escapeHtml(t)}">${escapeHtml(t)}</option>`).join('');
  filterTag.value = prev || '';
}

/* 상세 보기 (간단) */
window.viewDetail = function(id){
  const it = cachedEntries.find(x=>x.id===id);
  if (!it) return alert('항목을 찾을 수 없습니다.');
  let txt = `${it.title}\n\n${it.desc}\n\n태그: ${(it.tags||[]).join(', ')}\n\n속성:\n${Object.entries(it.stats||{}).map(([k,v])=>`${k}: ${v}`).join('\n')}`;
  alert(txt);
}

/* 관리자 접근(간단한 클라이언트 해시 검증) */
async function isAdminLogged(){
  return !!localStorage.getItem(ADMIN_TOKEN_KEY);
}
function setAdminToken(){ localStorage.setItem(ADMIN_TOKEN_KEY, 'tok_'+Math.random().toString(36).slice(2)); }
function clearAdminToken(){ localStorage.removeItem(ADMIN_TOKEN_KEY); }

/* admin 버튼 열기 */
openAdminBtn.addEventListener('click', () => {
  adminPanel.classList.remove('hidden');
  adminPanel.scrollTop = 0;
  adminMsg.textContent = '';
  adminPw.value = '';
  checkAdminState();
});
closeAdmin.addEventListener('click', ()=>{ adminPanel.classList.add('hidden'); });

async function checkAdminState(){
  if (await isAdminLogged()) {
    adminLoginSection.classList.add('hidden');
    adminWorkspace.classList.remove('hidden');
    adminState.textContent = '로그인';
    renderSubmissions();
  } else {
    adminLoginSection.classList.remove('hidden');
    adminWorkspace.classList.add('hidden');
    adminState.textContent = '로그아웃';
    adminMsg.textContent = '';
  }
}

/* 관리자 로그인 처리 */
adminLoginBtn.addEventListener('click', async ()=>{
  const pw = adminPw.value || '';
  if (!pw) { adminMsg.textContent = '비밀번호를 입력하세요.'; return; }
  const h = await sha256Hex(pw + SALT);
  if (h === STORED_HASH && STORED_HASH !== 'REPLACE_WITH_HASH') {
    setAdminToken();
    adminMsg.textContent = '로그인 성공';
    checkAdminState();
  } else {
    adminMsg.textContent = '비밀번호 오류';
  }
});

/* 로그아웃 */
logoutAdmin.addEventListener('click', ()=>{ clearAdminToken(); checkAdminState(); alert('로그아웃됨'); });

/* 해시 생성 버튼 (간단 안내) */
genHashBtn.addEventListener('click', async ()=>{ const p = prompt('새 관리자 비밀번호 입력:'); if (!p) return; await generateHash(p); });

howToSet.addEventListener('click', ()=> {
  alert('설정 절차:\n1) 콘솔에서 generateHash("원하는비밀번호") 실행\n2) 출력된 해시 값을 코드 상단 STORED_HASH 에 붙여넣기\n3) 파일 저장 후 브라우저 새로고침');
});

/* 데이터 내보내기/불러오기 */
exportData.addEventListener('click', ()=> {
  const data = { entries: cachedEntries, submissions: cachedSubs };
  const blob = new Blob([JSON.stringify(data, null, 2)], {type:'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = 'catalog_export.json'; a.click();
  URL.revokeObjectURL(url);
});
importData.addEventListener('click', ()=> {
  const file = document.createElement('input'); file.type='file'; file.accept='application/json';
  file.onchange = async (e) => {
    const f = e.target.files[0]; if(!f) return;
    const txt = await f.text();
    try {
      const parsed = JSON.parse(txt);
      if (parsed.entries) { cachedEntries = parsed.entries; saveJSON(ENTRY_KEY, cachedEntries); }
      if (parsed.submissions) { cachedSubs = parsed.submissions; saveJSON(SUB_KEY, cachedSubs); }
      renderAll();
      alert('데이터 불러오기 완료');
    } catch(e) { alert('파일 파싱 실패'); }
  };
  file.click();
});

/* 반려 항목 일괄 삭제 */
purgeRejected.addEventListener('click', ()=> {
  if (!confirm('반려 상태인 제출 항목을 모두 삭제하시겠습니까?')) return;
  cachedSubs = cachedSubs.filter(s => s.status !== 'rejected');
  saveJSON(SUB_KEY, cachedSubs); renderAll(); alert('처리 완료');
});

/* 초기 렌더 호출 */
function renderAll(){
  cachedEntries = loadJSON(ENTRY_KEY);
  cachedSubs = loadJSON(SUB_KEY);
  renderEntries();
  renderSubmissions();
  updateFilterTags();
}
renderAll();

/* 초기 메시지/폼 초기화 */
renderStats();
submitMsg.textContent = '';
document.addEventListener('visibilitychange', ()=>{ if (document.visibilityState==='visible') { cachedEntries = loadJSON(ENTRY_KEY); cachedSubs = loadJSON(SUB_KEY); updateFilterTags(); } });

/* 안전 알림(간단 안내) */
console.log('주의: 이 시스템은 로컬 클라이언트 기반입니다. 외부 공개용으로 사용할 경우 서버 기반 인증으로 전환하십시오.');

</script>
</body>
</html>
