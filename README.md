<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>GestãoRev — Devoluções & Trocas</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=DM+Mono:wght@300;400;500&display=swap" rel="stylesheet">

  <!-- 🔐 Credenciais do Firebase — NÃO commitado (ver .gitignore) -->
  <script src="firebase-config.js"></script>

  <script type="module">
/* ═══════════════════════════════════════════
   firebase.js — Inicialização + Auth + Firestore
   Expõe tudo via window._ para os outros módulos.
═══════════════════════════════════════════ */
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
import {
  getAuth,
  signInWithEmailAndPassword,
  signOut,
  onAuthStateChanged,
  createUserWithEmailAndPassword
} from "https://www.gstatic.com/firebasejs/10.12.0/firebase-auth.js";
import {
  getFirestore, collection, addDoc, getDocs,
  deleteDoc, doc, onSnapshot, query, orderBy, setDoc, getDoc, updateDoc
} from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";

const firebaseConfig = window.FIREBASE_CONFIG;

const app  = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db   = getFirestore(app);

// Firebase Auth
window._auth                      = auth;
window._signIn                    = signInWithEmailAndPassword;
window._signOut                   = signOut;
window._onAuthStateChanged        = onAuthStateChanged;
window._createUserWithEmail       = createUserWithEmailAndPassword;

// Firestore
window._db         = db;
window._col        = collection;
window._addDoc     = addDoc;
window._getDocs    = getDocs;
window._deleteDoc  = deleteDoc;
window._doc        = doc;
window._onSnapshot = onSnapshot;
window._query      = query;
window._orderBy    = orderBy;
window._setDoc     = setDoc;
window._getDoc      = getDoc;
window._updateDoc   = updateDoc;

document.dispatchEvent(new Event('firebase-ready'));

  </script>

  <style>
    /* ══ variables.css ══ */
/* ═══════════════════════════════════════════
   VARIÁVEIS GLOBAIS & RESET
   Para mudar cores, edite apenas este arquivo.
═══════════════════════════════════════════ */
:root {
  --bg:       #0e0e10;
  --surface:  #18181c;
  --surface2: #222228;
  --border:   #2e2e38;
  --text:     #f0f0f4;
  --muted:    #8888a0;

  --accent-dev:   #2563a8;
  --accent-troca: #4ecdc4;
  --accent-venda: #ffe66d;
  --admin:        #b57bff;
  --danger:       #ff4466;
  --success:      #44ffaa;

  --font-ui:   'DM Mono', monospace;
  --font-head: 'Syne', sans-serif;

  --radius-sm:   4px;
  --radius-md:   8px;
  --radius-lg:   16px;
  --radius-xl:   20px;
  --radius-pill: 999px;
}

*, *::before, *::after { margin:0; padding:0; box-sizing:border-box; }

body {
  background: var(--bg);
  color: var(--text);
  font-family: var(--font-ui);
  font-size: 13px;
  min-height: 100vh;
  overflow-x: hidden;
  -webkit-font-smoothing: antialiased;
}

body::before {
  content:'';
  position:fixed; inset:0;
  background-image:url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)' opacity='0.04'/%3E%3C/svg%3E");
  pointer-events:none; z-index:9999; opacity:0.4;
}

    /* ══ loading.css ══ */
/* LOADING */
#loading-screen {
  position:fixed; inset:0; background:var(--bg);
  display:flex; flex-direction:column; align-items:center; justify-content:center;
  gap:20px; z-index:99999;
}
.loader-logo { font-family:var(--font-head); font-size:32px; font-weight:800; }
.loader-logo span { color:var(--accent-dev); }
.loader-bar { width:200px; height:3px; background:var(--surface2); border-radius:var(--radius-pill); overflow:hidden; }
.loader-bar-fill { height:100%; background:var(--accent-troca); border-radius:var(--radius-pill); animation:loadbar 1.5s ease infinite; }
@keyframes loadbar {
  0%   { width:0;   margin-left:0; }
  50%  { width:60%; margin-left:20%; }
  100% { width:0;   margin-left:100%; }
}
.loader-txt { font-size:12px; color:var(--muted); }

    /* ══ login.css ══ */
/* LOGIN */
#login-screen { display:none; align-items:center; justify-content:center; min-height:100vh; padding:24px; }
#login-screen.visible { display:flex; }

.login-card {
  background:var(--surface); border:1px solid var(--border);
  border-radius:var(--radius-xl); padding:48px 40px;
  width:100%; max-width:420px; animation:fadeUp 0.4s ease;
}
@keyframes fadeUp { from{opacity:0;transform:translateY(20px)} to{opacity:1;transform:translateY(0)} }

.login-logo { font-family:var(--font-head); font-size:28px; font-weight:800; letter-spacing:-1px; margin-bottom:6px; }
.login-logo span { color:var(--accent-dev); }
.login-sub { font-size:12px; color:var(--muted); margin-bottom:36px; }

.login-field { display:flex; flex-direction:column; gap:6px; margin-bottom:16px; }
.login-field label { font-size:11px; color:var(--muted); text-transform:uppercase; letter-spacing:0.8px; }
.login-field input, .login-field select {
  background:var(--surface2); border:1px solid var(--border);
  border-radius:var(--radius-md); color:var(--text);
  font-family:var(--font-ui); font-size:14px; padding:12px 16px;
  outline:none; transition:border-color 0.2s;
}
.login-field input:focus, .login-field select:focus { border-color:var(--accent-troca); }

.btn-login {
  width:100%; margin-top:8px;
  font-family:var(--font-head); font-size:15px; font-weight:700;
  background:var(--accent-dev); color:#fff; border:none;
  border-radius:var(--radius-md); padding:14px; cursor:pointer; transition:all 0.2s;
}
.btn-login:hover { filter:brightness(1.15); transform:translateY(-1px); }
.btn-login:disabled { opacity:0.6; cursor:not-allowed; transform:none; }

.login-error {
  background:rgba(255,68,102,0.12); border:1px solid rgba(255,68,102,0.3);
  color:var(--danger); border-radius:var(--radius-md);
  padding:10px 14px; font-size:13px; margin-top:12px; display:none;
}
.login-error.show { display:block; }

    /* ══ header.css ══ */
/* APP + HEADER + TABS */
#app { display:none; }
#app.visible { display:block; }

header {
  padding:18px 40px; display:flex; align-items:center;
  justify-content:space-between; border-bottom:1px solid var(--border);
  flex-wrap:wrap; gap:12px;
}
.logo { font-family:var(--font-head); font-size:22px; font-weight:800; letter-spacing:-1px; }
.logo span { color:var(--accent-dev); }
.header-right { display:flex; align-items:center; gap:12px; flex-wrap:wrap; }

.stat-pill {
  display:flex; align-items:center; gap:8px;
  background:var(--surface2); border:1px solid var(--border);
  border-radius:var(--radius-pill); padding:6px 14px; font-size:12px;
}
.stat-pill .dot { width:8px; height:8px; border-radius:50%; }
.stat-pill .num { font-weight:500; font-size:14px; }

.user-badge {
  display:flex; align-items:center; gap:8px;
  background:var(--surface2); border:1px solid var(--border);
  border-radius:var(--radius-pill); padding:6px 14px;
}
.user-avatar {
  width:26px; height:26px; border-radius:50%;
  display:flex; align-items:center; justify-content:center;
  font-family:var(--font-head); font-size:10px; font-weight:800; color:#fff;
}
.user-name { font-family:var(--font-head); font-weight:700; font-size:13px; }
.user-role { color:var(--muted); font-size:11px; }

.btn-logout {
  background:none; border:1px solid var(--border); color:var(--muted);
  font-family:var(--font-head); font-size:12px; font-weight:600;
  border-radius:var(--radius-md); padding:7px 14px; cursor:pointer; transition:all 0.15s;
}
.btn-logout:hover { color:var(--danger); border-color:var(--danger); }

.sync-dot {
  width:8px; height:8px; border-radius:50%; background:var(--success);
  display:inline-block; margin-right:4px; animation:pulse 2s infinite;
}
@keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.3} }

/* Container */
.container { max-width:1400px; margin:0 auto; padding:32px 40px; }

/* Tabs */
.tabs {
  display:flex; gap:4px; flex-wrap:wrap;
  background:var(--surface); border:1px solid var(--border);
  border-radius:var(--radius-lg); padding:4px;
  margin-bottom:32px; width:fit-content;
}
.tab-btn {
  background:none; border:none; font-family:var(--font-head);
  font-size:13px; font-weight:600; color:var(--muted);
  padding:10px 22px; border-radius:var(--radius-md);
  cursor:pointer; transition:all 0.2s; display:flex; align-items:center; gap:8px;
}
.tab-btn.active { color:#fff; font-weight:700; }
.tab-btn[data-tab="devolucao"].active { background:var(--accent-dev); }
.tab-btn[data-tab="troca"].active     { background:var(--accent-troca); color:#000; }
.tab-btn[data-tab="venda"].active     { background:var(--accent-venda); color:#000; }
.tab-btn[data-tab="historico"].active { background:var(--text); color:#000; }
.tab-btn[data-tab="admin"].active     { background:var(--admin); color:#000; }

@media(max-width:768px) {
  header    { padding:16px 20px; }
  .container{ padding:20px; }
  .tabs     { width:100%; }
}

    /* ══ forms.css ══ */
/* FORMULÁRIOS */
.form-panel {
  background:var(--surface); border:1px solid var(--border);
  border-radius:var(--radius-lg); padding:32px; margin-bottom:32px; display:none;
}
.form-panel.active { display:block; }

.form-panel-header { display:flex; align-items:center; gap:12px; margin-bottom:28px; }
.form-panel-header h2 { font-family:var(--font-head); font-size:20px; font-weight:700; }
.form-panel-header .tag {
  font-family:var(--font-head); font-size:11px; font-weight:700;
  padding:4px 10px; border-radius:var(--radius-sm); letter-spacing:1px; text-transform:uppercase;
}
.tag-dev   { background:var(--accent-dev);   color:#fff; }
.tag-troca { background:var(--accent-troca); color:#000; }
.tag-venda { background:var(--accent-venda); color:#000; }

.form-grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(220px,1fr)); gap:16px; margin-bottom:20px; }

.field { display:flex; flex-direction:column; gap:6px; }
.field label { font-size:11px; color:var(--muted); text-transform:uppercase; letter-spacing:0.8px; }
.field input, .field select, .field textarea {
  background:var(--surface2); border:1px solid var(--border);
  border-radius:var(--radius-md); color:var(--text);
  font-family:var(--font-ui); font-size:14px; padding:10px 14px;
  outline:none; transition:border-color 0.2s;
}
.field input:focus, .field select:focus, .field textarea:focus { border-color:var(--accent-troca); }
.field textarea { resize:vertical; min-height:80px; }
select option { background:var(--surface2); }

.btn-submit {
  font-family:var(--font-head); font-size:14px; font-weight:700;
  border:none; border-radius:var(--radius-md); padding:12px 32px;
  cursor:pointer; transition:all 0.2s;
}
.btn-submit:hover  { transform:translateY(-1px); filter:brightness(1.1); }
.btn-submit:disabled { opacity:0.5; cursor:not-allowed; transform:none; }
.btn-dev   { background:var(--accent-dev);   color:#fff; }
.btn-troca { background:var(--accent-troca); color:#000; }
.btn-venda { background:var(--accent-venda); color:#000; }

/* Seção de fotos */
.photo-section {
  grid-column:1/-1; border:1px dashed var(--border);
  border-radius:var(--radius-lg); padding:20px; background:var(--surface2);
}
.photo-section-title { font-size:11px; color:var(--muted); text-transform:uppercase; letter-spacing:0.8px; margin-bottom:14px; }
.photo-actions { display:flex; gap:10px; flex-wrap:wrap; margin-bottom:16px; }
.btn-photo {
  font-family:var(--font-head); font-size:12px; font-weight:700;
  border:1px solid var(--border); border-radius:var(--radius-md);
  padding:9px 18px; cursor:pointer; background:var(--surface); color:var(--text); transition:all 0.2s;
}
.btn-photo:hover { border-color:var(--accent-troca); color:var(--accent-troca); }

.camera-container { display:none; flex-direction:column; align-items:flex-start; gap:12px; margin-bottom:16px; }
.camera-container.open { display:flex; }
.camera-container video { width:100%; max-width:360px; border-radius:var(--radius-md); border:2px solid var(--accent-troca); background:#000; }
.btn-capture { font-family:var(--font-head); font-size:13px; font-weight:700; background:var(--accent-troca); color:#000; border:none; border-radius:var(--radius-md); padding:10px 24px; cursor:pointer; }
.btn-cancel-cam { font-family:var(--font-head); font-size:12px; font-weight:600; background:none; color:var(--muted); border:1px solid var(--border); border-radius:var(--radius-md); padding:9px 18px; cursor:pointer; }

.photo-preview-grid { display:flex; flex-wrap:wrap; gap:10px; }
.photo-thumb { position:relative; width:90px; height:90px; border-radius:var(--radius-md); overflow:hidden; border:2px solid var(--border); cursor:pointer; transition:border-color 0.2s; }
.photo-thumb:hover { border-color:var(--accent-troca); }
.photo-thumb img { width:100%; height:100%; object-fit:cover; }
.photo-thumb .remove-photo {
  position:absolute; top:4px; right:4px; background:rgba(0,0,0,0.7); color:#fff;
  border:none; border-radius:50%; width:20px; height:20px; font-size:11px;
  cursor:pointer; display:flex; align-items:center; justify-content:center;
  opacity:0; transition:opacity 0.2s;
}
.photo-thumb:hover .remove-photo { opacity:1; }

@media(max-width:768px) { .form-grid { grid-template-columns:1fr; } }

    /* ══ table.css ══ */
/* HISTÓRICO */
#panel-historico { display:none; }
#panel-historico.active { display:block; }

.table-header { display:flex; align-items:center; justify-content:space-between; margin-bottom:16px; flex-wrap:wrap; gap:12px; }
.table-header h3 { font-family:var(--font-head); font-size:18px; font-weight:700; }
.search-bar {
  background:var(--surface); border:1px solid var(--border);
  border-radius:var(--radius-md); color:var(--text);
  font-family:var(--font-ui); font-size:13px; padding:8px 16px;
  width:280px; outline:none; transition:border-color 0.2s;
}
.search-bar:focus { border-color:var(--accent-troca); }

.filter-row { display:flex; gap:8px; margin-bottom:20px; flex-wrap:wrap; }
.filter-btn {
  background:var(--surface2); border:1px solid var(--border); color:var(--muted);
  font-family:var(--font-head); font-size:12px; font-weight:600;
  padding:6px 16px; border-radius:var(--radius-pill); cursor:pointer; transition:all 0.15s;
}
.filter-btn.f-dev.active-filter   { background:var(--accent-dev);   border-color:var(--accent-dev);   color:#fff; }
.filter-btn.f-troca.active-filter { background:var(--accent-troca); border-color:var(--accent-troca); color:#000; }
.filter-btn.f-venda.active-filter { background:var(--accent-venda); border-color:var(--accent-venda); color:#000; }
.filter-btn.active-filter { color:var(--text); border-color:var(--text); }

.records-table { width:100%; border-collapse:collapse; font-size:13px; }
.records-table thead tr { border-bottom:2px solid var(--border); }
.records-table th { text-align:left; font-family:var(--font-head); font-size:11px; font-weight:700; color:var(--muted); text-transform:uppercase; letter-spacing:1px; padding:10px 12px; white-space:nowrap; }
.records-table td { padding:13px 12px; border-bottom:1px solid var(--border); vertical-align:middle; }
.records-table tbody tr:hover { background:var(--surface2); }

.badge { display:inline-flex; align-items:center; font-family:var(--font-head); font-size:11px; font-weight:700; padding:3px 10px; border-radius:var(--radius-sm); letter-spacing:0.5px; text-transform:uppercase; white-space:nowrap; }
.badge-dev   { background:rgba(37,99,168,0.18);  color:var(--accent-dev);   border:1px solid rgba(37,99,168,0.35); }
.badge-troca { background:rgba(78,205,196,0.15); color:var(--accent-troca); border:1px solid rgba(78,205,196,0.30); }
.badge-venda { background:rgba(255,230,109,0.15);color:var(--accent-venda); border:1px solid rgba(255,230,109,0.30); }

.status-badge { display:inline-flex; align-items:center; gap:5px; font-size:11px; font-family:var(--font-head); font-weight:600; padding:3px 10px; border-radius:var(--radius-sm); white-space:nowrap; }
.status-badge::before { content:''; width:6px; height:6px; border-radius:50%; background:currentColor; }
.s-pendente  { color:var(--accent-venda); background:rgba(255,230,109,0.1); }
.s-resolvido { color:var(--success);      background:rgba(68,255,170,0.1); }
.s-andamento { color:var(--accent-troca); background:rgba(78,205,196,0.1); }

.author-chip { display:inline-flex; align-items:center; gap:7px; white-space:nowrap; }
.author-dot { width:22px; height:22px; border-radius:50%; display:flex; align-items:center; justify-content:center; font-family:var(--font-head); font-size:9px; font-weight:800; color:#fff; flex-shrink:0; }

.table-photos { display:flex; gap:4px; flex-wrap:wrap; }
.table-photo-thumb { width:36px; height:36px; border-radius:5px; object-fit:cover; border:1px solid var(--border); cursor:pointer; transition:border-color 0.2s; }
.table-photo-thumb:hover { border-color:var(--accent-troca); }
.no-photo { color:var(--muted); font-size:12px; }

.btn-delete { background:none; border:none; cursor:pointer; color:var(--muted); font-size:14px; padding:4px 8px; border-radius:var(--radius-sm); transition:all 0.15s; }
.btn-delete:hover { color:var(--danger); background:rgba(255,68,102,0.1); }

.empty-state { text-align:center; padding:60px 0; color:var(--muted); }
.empty-state .icon { font-size:48px; margin-bottom:12px; }
.empty-state p { font-family:var(--font-head); font-size:14px; }

@media(max-width:768px) { .records-table{font-size:12px;} .search-bar{width:100%;} }

    /* ══ admin.css ══ */
/* ADMIN */
#panel-admin { display:none; }
#panel-admin.active { display:block; }

.stats-grid { display:grid; grid-template-columns:repeat(3,1fr); gap:12px; margin-bottom:24px; }
.stat-card { background:var(--surface); border:1px solid var(--border); border-radius:var(--radius-md); padding:16px; text-align:center; }
.stat-card .val { font-family:var(--font-head); font-size:28px; font-weight:800; }
.stat-card .lbl { font-size:11px; color:var(--muted); margin-top:4px; text-transform:uppercase; }

.admin-grid { display:grid; grid-template-columns:1fr 1fr; gap:24px; }
.admin-card { background:var(--surface); border:1px solid var(--border); border-radius:var(--radius-lg); padding:28px; }
.admin-card h3 { font-family:var(--font-head); font-size:16px; font-weight:700; margin-bottom:20px; }

.func-list { display:flex; flex-direction:column; gap:10px; margin-bottom:20px; max-height:340px; overflow-y:auto; }
.func-item { display:flex; align-items:center; justify-content:space-between; background:var(--surface2); border:1px solid var(--border); border-radius:var(--radius-md); padding:12px 16px; }
.func-item-left { display:flex; align-items:center; gap:12px; }
.func-avatar { width:32px; height:32px; border-radius:50%; display:flex; align-items:center; justify-content:center; font-family:var(--font-head); font-size:12px; font-weight:800; color:#fff; flex-shrink:0; }
.func-info .func-name { font-family:var(--font-head); font-size:14px; font-weight:700; }
.func-info .func-meta { font-size:11px; color:var(--muted); margin-top:2px; }

.role-badge { font-family:var(--font-head); font-size:10px; font-weight:700; padding:2px 8px; border-radius:var(--radius-sm); text-transform:uppercase; }
.role-admin { background:rgba(181,123,255,0.2); color:var(--admin);        border:1px solid rgba(181,123,255,0.3); }
.role-func  { background:rgba(78,205,196,0.15); color:var(--accent-troca); border:1px solid rgba(78,205,196,0.25); }

.btn-func-del { background:none; border:1px solid var(--border); color:var(--muted); border-radius:6px; padding:5px 10px; cursor:pointer; font-size:12px; transition:all 0.15s; }
.btn-func-del:hover { color:var(--danger); border-color:var(--danger); }

.add-func-form { display:flex; flex-direction:column; gap:12px; }
.add-func-form input, .add-func-form select {
  background:var(--surface2); border:1px solid var(--border);
  border-radius:var(--radius-md); color:var(--text);
  font-family:var(--font-ui); font-size:14px; padding:10px 14px;
  outline:none; transition:border-color 0.2s;
}
.add-func-form input:focus { border-color:var(--accent-troca); }
.btn-add-func { font-family:var(--font-head); font-size:13px; font-weight:700; background:var(--admin); color:#000; border:none; border-radius:var(--radius-md); padding:11px 24px; cursor:pointer; transition:all 0.2s; width:fit-content; }
.btn-add-func:hover { filter:brightness(1.1); }

/* Nota sobre e-mail */
.field-hint { font-size:11px; color:var(--muted); margin-top:4px; }

@media(max-width:900px) { .admin-grid{grid-template-columns:1fr;} .stats-grid{grid-template-columns:1fr 1fr;} }

    /* ══ components.css ══ */
/* LIGHTBOX */
.lightbox { display:none; position:fixed; inset:0; background:rgba(0,0,0,0.93); z-index:10000; align-items:center; justify-content:center; flex-direction:column; gap:16px; }
.lightbox.open { display:flex; }
.lightbox img { max-width:90vw; max-height:80vh; border-radius:var(--radius-lg); }
.lightbox-nav { display:flex; gap:12px; }
.lightbox-nav button, .lightbox-close { font-family:var(--font-head); font-size:13px; font-weight:700; background:var(--surface); color:var(--text); border:1px solid var(--border); border-radius:var(--radius-md); padding:9px 20px; cursor:pointer; transition:all 0.15s; }
.lightbox-nav button:hover { border-color:var(--accent-troca); color:var(--accent-troca); }

/* TOAST */
.toast { position:fixed; bottom:24px; right:24px; font-family:var(--font-head); font-size:13px; font-weight:700; padding:12px 24px; border-radius:var(--radius-md); transform:translateY(20px); opacity:0; transition:all 0.25s ease; z-index:10001; pointer-events:none; }
.toast.show { transform:translateY(0); opacity:1; }
.toast-success { background:var(--success); color:#000; }
.toast-error   { background:var(--danger);  color:#fff; }



    /* ══ dashboard ══ */
    #panel-dashboard { display:none; }
    #panel-dashboard.active { display:block; }
    .dash-header { display:flex; align-items:center; justify-content:space-between; margin-bottom:24px; flex-wrap:wrap; gap:12px; }
    .dash-header h3 { font-family:var(--font-head); font-size:18px; font-weight:700; }
    .dash-period-btns { display:flex; gap:6px; }
    .dash-period-btn { font-family:var(--font-head); font-size:12px; font-weight:700; background:var(--surface2); border:1px solid var(--border); color:var(--muted); border-radius:var(--radius-md); padding:7px 16px; cursor:pointer; transition:all 0.15s; }
    .dash-period-btn.active { background:var(--accent-dev); border-color:var(--accent-dev); color:#fff; }
    .dash-kpis { display:grid; grid-template-columns:repeat(4,1fr); gap:12px; margin-bottom:28px; }
    .dash-kpi { background:var(--surface); border:1px solid var(--border); border-radius:var(--radius-lg); padding:20px; display:flex; flex-direction:column; gap:6px; }
    .dash-kpi .kpi-val { font-family:var(--font-head); font-size:32px; font-weight:800; line-height:1; }
    .dash-kpi .kpi-lbl { font-size:11px; color:var(--muted); text-transform:uppercase; letter-spacing:0.8px; }
    .kpi-trend { font-size:11px; font-family:var(--font-head); font-weight:700; margin-top:4px; }
    .kpi-trend.up { color:var(--success); } .kpi-trend.down { color:var(--danger); }
    .dash-charts-row { display:grid; grid-template-columns:2fr 1fr; gap:20px; margin-bottom:20px; }
    .dash-charts-row2 { display:grid; grid-template-columns:1fr 1fr; gap:20px; margin-bottom:20px; }
    .dash-card { background:var(--surface); border:1px solid var(--border); border-radius:var(--radius-lg); padding:24px; }
    .dash-card h4 { font-family:var(--font-head); font-size:11px; font-weight:700; margin-bottom:20px; color:var(--muted); text-transform:uppercase; letter-spacing:0.8px; }
    .top-produtos { display:flex; flex-direction:column; gap:10px; }
    .top-produto-item { display:flex; align-items:center; gap:12px; }
    .top-produto-rank { font-family:var(--font-head); font-size:12px; font-weight:800; color:var(--muted); width:20px; text-align:center; }
    .top-produto-bar-wrap { flex:1; background:var(--surface2); border-radius:var(--radius-pill); height:8px; overflow:hidden; }
    .top-produto-bar { height:100%; border-radius:var(--radius-pill); transition:width 0.5s ease; }
    .top-produto-nome { font-size:12px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; max-width:160px; }
    .top-produto-count { font-family:var(--font-head); font-size:13px; font-weight:700; min-width:24px; text-align:right; }
    @media(max-width:900px) { .dash-kpis{grid-template-columns:1fr 1fr;} .dash-charts-row{grid-template-columns:1fr;} .dash-charts-row2{grid-template-columns:1fr;} }

    /* ══ comments modal ══ */
    .modal-overlay { position:fixed; inset:0; background:rgba(0,0,0,0.7); z-index:10000; display:flex; align-items:center; justify-content:center; padding:20px; animation:fadeIn 0.2s ease; }
    @keyframes fadeIn { from{opacity:0} to{opacity:1} }
    .modal-box { background:var(--surface); border:1px solid var(--border); border-radius:var(--radius-xl); width:100%; max-width:540px; max-height:80vh; display:flex; flex-direction:column; animation:slideUp 0.25s ease; }
    @keyframes slideUp { from{opacity:0;transform:translateY(20px)} to{opacity:1;transform:translateY(0)} }
    .modal-header { padding:20px 24px 16px; border-bottom:1px solid var(--border); display:flex; align-items:flex-start; justify-content:space-between; gap:12px; }
    .modal-header h3 { font-family:var(--font-head); font-size:16px; font-weight:700; }
    .modal-header p { font-size:11px; color:var(--muted); margin-top:3px; }
    .modal-close { background:none; border:none; color:var(--muted); font-size:20px; cursor:pointer; padding:0 4px; line-height:1; transition:color 0.15s; }
    .modal-close:hover { color:var(--text); }
    .modal-body { padding:20px 24px; overflow-y:auto; flex:1; display:flex; flex-direction:column; gap:16px; }
    .comment-list { display:flex; flex-direction:column; gap:12px; }
    .comment-item { background:var(--surface2); border:1px solid var(--border); border-radius:var(--radius-md); padding:14px 16px; }
    .comment-top { display:flex; align-items:center; gap:10px; margin-bottom:8px; }
    .comment-avatar { width:28px; height:28px; border-radius:50%; display:flex; align-items:center; justify-content:center; font-family:var(--font-head); font-size:10px; font-weight:800; color:#fff; flex-shrink:0; }
    .comment-meta { flex:1; }
    .comment-autor { font-family:var(--font-head); font-size:12px; font-weight:700; }
    .comment-time { font-size:10px; color:var(--muted); margin-top:1px; }
    .comment-text { font-size:12px; line-height:1.6; color:var(--text); white-space:pre-wrap; }
    .comment-empty { text-align:center; padding:24px; color:var(--muted); font-size:12px; }
    .comment-form { display:flex; flex-direction:column; gap:10px; padding-top:8px; border-top:1px solid var(--border); }
    .comment-form textarea { background:var(--surface2); border:1px solid var(--border); border-radius:var(--radius-md); color:var(--text); font-family:var(--font-ui); font-size:13px; padding:12px 14px; resize:none; height:80px; outline:none; transition:border-color 0.2s; }
    .comment-form textarea:focus { border-color:var(--accent-troca); }
    .btn-comment-send { align-self:flex-end; background:var(--accent-dev); border:none; color:#fff; font-family:var(--font-head); font-size:13px; font-weight:700; padding:10px 24px; border-radius:var(--radius-md); cursor:pointer; transition:opacity 0.15s; }
    .btn-comment-send:hover { opacity:0.85; }
    .btn-comment-send:disabled { opacity:0.4; cursor:not-allowed; }
    .btn-comments { background:none; border:1px solid var(--border); color:var(--muted); font-family:var(--font-head); font-size:11px; font-weight:700; padding:4px 10px; border-radius:var(--radius-sm); cursor:pointer; transition:all 0.15s; white-space:nowrap; }
    .btn-comments:hover { border-color:var(--accent-troca); color:var(--accent-troca); }
    .btn-comments.has-comments { border-color:var(--accent-troca); color:var(--accent-troca); }

  </style>
</head>
<body>


<!-- LOADING -->
<div id="loading-screen">
  <div class="loader-logo">Gestão<span>Rev</span></div>
  <div class="loader-bar"><div class="loader-bar-fill"></div></div>
  <div class="loader-txt">Conectando ao Firebase...</div>
</div>

<!-- LOGIN -->
<div id="login-screen">
  <div class="login-card">
    <div class="login-logo">Gestão<span>Rev</span></div>
    <div class="login-sub">Devoluções · Trocas · Vendas Erradas</div>

    <div class="login-field">
      <label>E-mail</label>
      <input type="email" id="login-email" placeholder="seu@email.com"
        onkeydown="if(event.key==='Enter') fazerLogin()">
    </div>
    <div class="login-field">
      <label>Senha</label>
      <input type="password" id="login-senha" placeholder="••••••"
        onkeydown="if(event.key==='Enter') fazerLogin()">
    </div>

    <button class="btn-login" id="btn-login" onclick="fazerLogin()">Entrar →</button>
    <div class="login-error" id="login-error"></div>
  </div>
</div>

<!-- APP -->
<div id="app">
  <header>
    <div style="display:flex;align-items:center;gap:16px">
      <div class="logo">Gestão<span>Rev</span></div>
      
    </div>
    <div class="header-right">
      <div class="stat-pill"><div class="dot" style="background:var(--accent-dev)"></div><span style="font-size:11px">Dev</span><span class="num" id="count-dev">0</span></div>
      <div class="stat-pill"><div class="dot" style="background:var(--accent-troca)"></div><span style="font-size:11px">Trocas</span><span class="num" id="count-troca">0</span></div>
      <div class="stat-pill"><div class="dot" style="background:var(--accent-venda)"></div><span style="font-size:11px">Vendas</span><span class="num" id="count-venda">0</span></div>
      <div class="user-badge">
        <div class="user-avatar" id="user-avatar-hdr"></div>
        <div><div class="user-name" id="user-name-hdr"></div><div class="user-role" id="user-role-hdr"></div></div>
      </div>
      <button class="btn-logout" onclick="fazerLogout()">Sair</button>
    </div>
  </header>

  <div class="container">
    <div class="tabs">
      <button class="tab-btn active"    data-tab="dashboard">📊 Dashboard</button>
        <button class="tab-btn"           data-tab="devolucao">↩ Devolução</button>
      <button class="tab-btn"           data-tab="troca">⇄ Troca</button>
      <button class="tab-btn"           data-tab="venda">⚠ Venda Errada</button>
      <button class="tab-btn"           data-tab="historico">≡ Histórico</button>
      <button class="tab-btn tab-admin" data-tab="admin" style="display:none">⚙ Admin</button>
    </div>

    <!-- DEVOLUÇÃO -->
    <div class="form-panel active" id="panel-devolucao">
      <div class="form-panel-header"><span class="tag tag-dev">Devolução</span><h2>Registrar Item Devolvido</h2></div>
      <div class="form-grid">
        <div class="field"><label>SKU / Código</label><input type="text" id="dev-sku" placeholder="ex: SKU-1234"></div>
        <div class="field"><label>Nome do Produto</label><input type="text" id="dev-nome" placeholder="ex: Camiseta Azul P"></div>
        <div class="field"><label>Quantidade</label><input type="number" id="dev-qtd" min="1" value="1"></div>
        <div class="field"><label>Cliente</label><input type="text" id="dev-cliente" placeholder="Nome ou CPF"></div>
        <div class="field"><label>Motivo</label>
          <select id="dev-motivo"><option value="">Selecione...</option><option>Produto com defeito</option><option>Produto errado enviado</option><option>Não gostou / desistência</option><option>Tamanho/cor incorretos</option><option>Produto danificado na entrega</option><option>Outro</option></select>
        </div>
        <div class="field"><label>Status</label>
          <select id="dev-status"><option value="pendente">Pendente</option><option value="andamento">Em Andamento</option><option value="resolvido">Resolvido</option></select>
        </div>
        <div class="field" style="grid-column:1/-1"><label>Observações</label><textarea id="dev-obs" placeholder="Detalhes adicionais..."></textarea></div>
        <div class="photo-section">
          <div class="photo-section-title">📷 Fotos do Item</div>
          <div class="photo-actions">
            <button class="btn-photo" onclick="abrirCamera('dev')">📷 Tirar Foto</button>
            <button class="btn-photo" onclick="document.getElementById('dev-file').click()">🖼 Carregar</button>
            <input type="file" id="dev-file" accept="image/*" multiple style="display:none" onchange="carregarArquivo(event,'dev')">
          </div>
          <div class="camera-container" id="cam-dev">
            <video id="video-dev" autoplay playsinline></video>
            <div style="display:flex;gap:10px"><button class="btn-capture" onclick="capturar('dev')">📸 Capturar</button><button class="btn-cancel-cam" onclick="fecharCamera('dev')">Cancelar</button></div>
          </div>
          <div class="photo-preview-grid" id="preview-dev"></div>
          <canvas id="canvas-dev" style="display:none"></canvas>
        </div>
      </div>
      <button class="btn-submit btn-dev" id="btn-dev" onclick="registrar('devolucao')">↩ Registrar Devolução</button>
    </div>

    <!-- TROCA -->
    <div class="form-panel" id="panel-troca">
      <div class="form-panel-header"><span class="tag tag-troca">Troca</span><h2>Registrar Troca</h2></div>
      <div class="form-grid">
        <div class="field"><label>SKU Original</label><input type="text" id="troca-sku-orig" placeholder="ex: SKU-1234"></div>
        <div class="field"><label>Nome Original</label><input type="text" id="troca-nome-orig" placeholder="ex: Tênis Preto 42"></div>
        <div class="field"><label>SKU Substituto</label><input type="text" id="troca-sku-sub" placeholder="ex: SKU-5678"></div>
        <div class="field"><label>Nome Substituto</label><input type="text" id="troca-nome-sub" placeholder="ex: Tênis Preto 43"></div>
        <div class="field"><label>Quantidade</label><input type="number" id="troca-qtd" min="1" value="1"></div>
        <div class="field"><label>Cliente</label><input type="text" id="troca-cliente" placeholder="Nome ou CPF"></div>
        <div class="field"><label>Motivo</label>
          <select id="troca-motivo"><option value="">Selecione...</option><option>Tamanho errado</option><option>Cor errada</option><option>Produto com defeito</option><option>Preferência do cliente</option><option>Outro</option></select>
        </div>
        <div class="field"><label>Status</label>
          <select id="troca-status"><option value="pendente">Pendente</option><option value="andamento">Em Andamento</option><option value="resolvido">Resolvido</option></select>
        </div>
        <div class="field" style="grid-column:1/-1"><label>Observações</label><textarea id="troca-obs" placeholder="Detalhes adicionais..."></textarea></div>
        <div class="photo-section">
          <div class="photo-section-title">📷 Fotos da Troca</div>
          <div class="photo-actions">
            <button class="btn-photo" onclick="abrirCamera('troca')">📷 Tirar Foto</button>
            <button class="btn-photo" onclick="document.getElementById('troca-file').click()">🖼 Carregar</button>
            <input type="file" id="troca-file" accept="image/*" multiple style="display:none" onchange="carregarArquivo(event,'troca')">
          </div>
          <div class="camera-container" id="cam-troca">
            <video id="video-troca" autoplay playsinline></video>
            <div style="display:flex;gap:10px"><button class="btn-capture" onclick="capturar('troca')">📸 Capturar</button><button class="btn-cancel-cam" onclick="fecharCamera('troca')">Cancelar</button></div>
          </div>
          <div class="photo-preview-grid" id="preview-troca"></div>
          <canvas id="canvas-troca" style="display:none"></canvas>
        </div>
      </div>
      <button class="btn-submit btn-troca" id="btn-troca" onclick="registrar('troca')">⇄ Registrar Troca</button>
    </div>

    <!-- VENDA ERRADA -->
    <div class="form-panel" id="panel-venda">
      <div class="form-panel-header"><span class="tag tag-venda">Venda Errada</span><h2>Registrar Venda Incorreta</h2></div>
      <div class="form-grid">
        <div class="field"><label>Nº Pedido / NF</label><input type="text" id="venda-pedido" placeholder="ex: PED-00321"></div>
        <div class="field"><label>SKU</label><input type="text" id="venda-sku" placeholder="ex: SKU-9999"></div>
        <div class="field"><label>Nome do Produto</label><input type="text" id="venda-nome" placeholder="ex: Calça Jeans 38"></div>
        <div class="field"><label>Quantidade</label><input type="number" id="venda-qtd" min="1" value="1"></div>
        <div class="field"><label>Cliente</label><input type="text" id="venda-cliente" placeholder="Nome ou CPF"></div>
        <div class="field"><label>Tipo de Erro</label>
          <select id="venda-tipo"><option value="">Selecione...</option><option>Produto errado cobrado</option><option>Valor incorreto</option><option>Quantidade errada</option><option>Desconto não aplicado</option><option>Produto duplicado</option><option>Outro</option></select>
        </div>
        <div class="field"><label>Status</label>
          <select id="venda-status"><option value="pendente">Pendente</option><option value="andamento">Em Andamento</option><option value="resolvido">Resolvido</option></select>
        </div>
        <div class="field" style="grid-column:1/-1"><label>Descrição do Erro</label><textarea id="venda-obs" placeholder="Descreva o erro..."></textarea></div>
        <div class="photo-section">
          <div class="photo-section-title">📷 Fotos da Venda Errada</div>
          <div class="photo-actions">
            <button class="btn-photo" onclick="abrirCamera('venda')">📷 Tirar Foto</button>
            <button class="btn-photo" onclick="document.getElementById('venda-file').click()">🖼 Carregar</button>
            <input type="file" id="venda-file" accept="image/*" multiple style="display:none" onchange="carregarArquivo(event,'venda')">
          </div>
          <div class="camera-container" id="cam-venda">
            <video id="video-venda" autoplay playsinline></video>
            <div style="display:flex;gap:10px"><button class="btn-capture" onclick="capturar('venda')">📸 Capturar</button><button class="btn-cancel-cam" onclick="fecharCamera('venda')">Cancelar</button></div>
          </div>
          <div class="photo-preview-grid" id="preview-venda"></div>
          <canvas id="canvas-venda" style="display:none"></canvas>
        </div>
      </div>
      <button class="btn-submit btn-venda" id="btn-venda" onclick="registrar('venda')">⚠ Registrar Venda Errada</button>
    </div>

    <!-- MODAL COMENTÁRIOS -->
    <div class="modal-overlay" id="modal-comentarios" style="display:none">
      <div class="modal-box" onclick="event.stopPropagation()">
        <div class="modal-header">
          <div><h3>💬 Histórico de Tratativas</h3><p id="modal-produto-nome">—</p></div>
          <button class="modal-close" onclick="document.getElementById('modal-comentarios').style.display='none';if(window.comentariosUnsub){window.comentariosUnsub();}">✕</button>
        </div>
        <div class="modal-body">
          <div class="comment-list" id="comment-list"><div class="comment-empty">Nenhum comentário ainda.</div></div>
          <div class="comment-form">
            <textarea id="comment-input" placeholder="Adicione uma anotação ou atualização..."></textarea>
            <button class="btn-comment-send" id="btn-send-comment" onclick="enviarComentario()">Enviar →</button>
          </div>
        </div>
      </div>
    </div>

    <!-- DASHBOARD -->
    <div id="panel-dashboard">
      <div class="dash-header">
        <h3>📊 Dashboard</h3>
        <div class="dash-period-btns">
          <button class="dash-period-btn active" onclick="setPeriodo('semana',this)">Esta semana</button>
          <button class="dash-period-btn" onclick="setPeriodo('mes',this)">Este mês</button>
          <button class="dash-period-btn" onclick="setPeriodo('tudo',this)">Tudo</button>
        </div>
      </div>
      <div class="dash-kpis">
        <div class="dash-kpi" style="border-top:3px solid var(--accent-dev)">
          <div class="kpi-val" id="kpi-dev">0</div><div class="kpi-lbl">Devoluções</div><div class="kpi-trend" id="kpi-dev-trend"></div>
        </div>
        <div class="dash-kpi" style="border-top:3px solid var(--accent-troca)">
          <div class="kpi-val" id="kpi-troca">0</div><div class="kpi-lbl">Trocas</div><div class="kpi-trend" id="kpi-troca-trend"></div>
        </div>
        <div class="dash-kpi" style="border-top:3px solid var(--accent-venda)">
          <div class="kpi-val" id="kpi-venda">0</div><div class="kpi-lbl">Vendas Erradas</div><div class="kpi-trend" id="kpi-venda-trend"></div>
        </div>
        <div class="dash-kpi" style="border-top:3px solid var(--danger)">
          <div class="kpi-val" id="kpi-pendentes">0</div><div class="kpi-lbl">Pendentes</div><div class="kpi-trend" id="kpi-pendentes-trend"></div>
        </div>
      </div>
      <div class="dash-charts-row">
        <div class="dash-card"><h4>Registros ao longo do tempo</h4><canvas id="chart-linha" height="100"></canvas></div>
        <div class="dash-card"><h4>Distribuição por tipo</h4><canvas id="chart-donut" height="200"></canvas></div>
      </div>
      <div class="dash-charts-row2">
        <div class="dash-card"><h4>🏆 Produtos mais registrados</h4><div class="top-produtos" id="top-produtos"></div></div>
        <div class="dash-card"><h4>Status geral</h4><canvas id="chart-status" height="200"></canvas></div>
      </div>
    </div>

    <!-- HISTÓRICO -->
    <div id="panel-historico">
      <div class="table-header">
        <h3>Histórico de Registros</h3>
        <input class="search-bar" type="text" placeholder="🔍  Produto, cliente, funcionário..." oninput="filtrarTabela(this.value)">
      </div>
      <div class="filter-row">
        <button class="filter-btn active-filter" onclick="setFiltro('todos',this)">Todos</button>
        <button class="filter-btn f-dev"   onclick="setFiltro('devolucao',this)">↩ Devoluções</button>
        <button class="filter-btn f-troca" onclick="setFiltro('troca',this)">⇄ Trocas</button>
        <button class="filter-btn f-venda" onclick="setFiltro('venda',this)">⚠ Vendas Erradas</button>
      </div>
      <table class="records-table">
        <thead><tr>
          <th>#</th><th>Tipo</th><th>Produto</th><th>Cliente</th><th>Qtd</th>
          <th>Motivo</th><th>Fotos</th><th>Registrado por</th><th>Status</th><th>Data/Hora</th><th></th>
        </tr></thead>
        <tbody id="tabela-body"></tbody>
      </table>
      <div class="empty-state" id="empty-state"><div class="icon">📦</div><p>Nenhum registro encontrado.</p></div>
    </div>

    <!-- ADMIN -->
    <div id="panel-admin">
      <div class="stats-grid">
        <div class="stat-card"><div class="val" id="adm-total">0</div><div class="lbl">Total Registros</div></div>
        <div class="stat-card"><div class="val" id="adm-pendentes">0</div><div class="lbl">Pendentes</div></div>
        <div class="stat-card"><div class="val" id="adm-funcs">0</div><div class="lbl">Funcionários</div></div>
      </div>
      <div class="admin-grid">
        <div class="admin-card">
          <h3>👥 Funcionários Cadastrados</h3>
          <div class="func-list" id="func-list"></div>
        </div>
        <div class="admin-card">
          <h3>➕ Novo Funcionário</h3>
          <div class="add-func-form">
            <input type="text"     id="nf-nome"  placeholder="Nome completo">
            <input type="email"    id="nf-email" placeholder="email@empresa.com">
            <div>
              <input type="password" id="nf-senha" placeholder="Senha (mín. 6 caracteres)">
              <div class="field-hint">A senha fica protegida pelo Google — nunca aparece no banco de dados.</div>
            </div>
            <select id="nf-role">
              <option value="funcionario">Funcionário</option>
              <option value="admin">Administrador</option>
            </select>
            <button class="btn-add-func" onclick="adicionarFuncionario()">➕ Adicionar</button>
          </div>
        </div>
      </div>
    </div>

  </div><!-- /container -->
</div><!-- /app -->

<!-- LIGHTBOX -->
<div class="lightbox" id="lightbox" onclick="fecharLightbox(event)">
  <img id="lightbox-img" src="" alt="">
  <div class="lightbox-nav">
    <button onclick="lightboxNav(-1);event.stopPropagation()">← Anterior</button>
    <button class="lightbox-close" onclick="fecharLightbox()">✕ Fechar</button>
    <button onclick="lightboxNav(1);event.stopPropagation()">Próxima →</button>
  </div>
</div>

<div class="toast" id="toast"></div>

<!-- SCRIPTS (ordem importa) -->


  <script>
    /* ══ utils.js ══ */
/* utils.js — Funções auxiliares */

const AV_CORES = ['#2563a8','#4ecdc4','#ffe66d','#b57bff','#44ffaa','#1e7abf','#54a0ff','#ff6b81','#a29bfe','#fd79a8'];

function corNome(nome) {
  let h = 0;
  for (const c of (nome || 'A')) h = (h * 31 + c.charCodeAt(0)) % AV_CORES.length;
  return AV_CORES[h];
}

function iniciais(nome) {
  return (nome || '?').split(' ').slice(0, 2).map(p => p[0]).join('').toUpperCase();
}

function showToast(msg, tipo = 'success') {
  const el = document.getElementById('toast');
  el.textContent = msg;
  el.className = `toast toast-${tipo} show`;
  setTimeout(() => el.classList.remove('show'), 3000);
}

    /* ══ auth.js ══ */
/* ═══════════════════════════════════════════
   auth.js
   Login e logout usando Firebase Authentication.
   As senhas ficam protegidas pelo Google —
   NUNCA são salvas no Firestore.
═══════════════════════════════════════════ */

let usuarioLogado = null; // perfil do Firestore (nome, role, etc.)

function getUsuario() { return usuarioLogado; }

/**
 * Login: autentica pelo Firebase Auth (e-mail + senha).
 * Depois busca o perfil completo no Firestore (nome, cargo, role).
 */
async function fazerLogin() {
  const email = document.getElementById('login-email').value.trim();
  const senha  = document.getElementById('login-senha').value;
  const errEl  = document.getElementById('login-error');
  const btn    = document.getElementById('btn-login');

  if (!email || !senha) {
    errEl.textContent = 'Preencha e-mail e senha.';
    errEl.classList.add('show'); return;
  }

  btn.disabled = true;
  btn.textContent = 'Entrando...';

  try {
    // 1. Autentica com Firebase Auth
    const cred = await window._signIn(window._auth, email, senha);

    // 2. Busca perfil no Firestore
    const perfil = await _buscarPerfil(cred.user.uid);
    if (!perfil) throw new Error('Perfil não encontrado.');

    usuarioLogado = perfil;
    errEl.classList.remove('show');
    _exibirApp();

  } catch (e) {
    let msg = 'E-mail ou senha incorretos.';
    if (e.code === 'auth/too-many-requests') msg = 'Muitas tentativas. Aguarde alguns minutos.';
    if (e.code === 'auth/user-disabled')     msg = 'Conta desativada. Fale com o administrador.';
    errEl.textContent = msg;
    errEl.classList.add('show');
  }

  btn.disabled = false;
  btn.textContent = 'Entrar →';
}

/** Busca o perfil do usuário no Firestore pela UID do Firebase Auth. */
async function _buscarPerfil(uid) {
  const snap = await window._getDoc(window._doc(window._db, 'funcionarios', uid));
  return snap.exists() ? { ...snap.data(), _uid: uid } : null;
}

/** Exibe o app e configura o header com os dados do usuário. */
function _exibirApp() {
  document.getElementById('login-screen').classList.remove('visible');
  document.getElementById('app').classList.add('visible');

  const cor = corNome(usuarioLogado.nome);
  const av  = document.getElementById('user-avatar-hdr');
  av.style.background = cor;
  av.textContent      = iniciais(usuarioLogado.nome);

  document.getElementById('user-name-hdr').textContent = usuarioLogado.nome;
  document.getElementById('user-role-hdr').textContent =
    usuarioLogado.role === 'admin' ? 'Administrador' : 'Funcionário';

  document.querySelector('.tab-admin').style.display =
    usuarioLogado.role === 'admin' ? 'flex' : 'none';
}

/** Desloga do Firebase Auth e volta para a tela de login. */
async function fazerLogout() {
  await window._signOut(window._auth);
  usuarioLogado = null;
  document.getElementById('app').classList.remove('visible');
  document.getElementById('login-screen').classList.add('visible');
  document.getElementById('login-senha').value = '';
  document.getElementById('login-email').value = '';
  Object.keys(window._streams || {}).forEach(k => fecharCamera(k));
}

    /* ══ camera.js ══ */
/* camera.js — Câmera e upload de fotos */

const fotos   = { dev: [], troca: [], venda: [] };
const streams = {};
window._streams = streams;

async function abrirCamera(prefix) {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' }, audio: false });
    streams[prefix] = stream;
    document.getElementById('video-' + prefix).srcObject = stream;
    document.getElementById('cam-' + prefix).classList.add('open');
  } catch (e) { alert('Não foi possível acessar a câmera.'); }
}

function fecharCamera(prefix) {
  const cam = document.getElementById('cam-' + prefix);
  const vid = document.getElementById('video-' + prefix);
  if (!cam) return;
  if (streams[prefix]) { streams[prefix].getTracks().forEach(t => t.stop()); delete streams[prefix]; }
  if (vid) vid.srcObject = null;
  cam.classList.remove('open');
}

function capturar(prefix) {
  const v = document.getElementById('video-' + prefix);
  const c = document.getElementById('canvas-' + prefix);
  c.width = v.videoWidth; c.height = v.videoHeight;
  c.getContext('2d').drawImage(v, 0, 0);
  adicionarFoto(prefix, c.toDataURL('image/jpeg', 0.6));
}

function carregarArquivo(event, prefix) {
  Array.from(event.target.files).forEach(file => {
    const img = new Image(), reader = new FileReader();
    reader.onload = e => {
      img.onload = () => {
        const canvas = document.createElement('canvas');
        const MAX = 800; let w = img.width, h = img.height;
        if (w > MAX) { h = Math.round(h * MAX / w); w = MAX; }
        canvas.width = w; canvas.height = h;
        canvas.getContext('2d').drawImage(img, 0, 0, w, h);
        adicionarFoto(prefix, canvas.toDataURL('image/jpeg', 0.6));
      };
      img.src = e.target.result;
    };
    reader.readAsDataURL(file);
  });
  event.target.value = '';
}

function adicionarFoto(prefix, dataUrl) { fotos[prefix].push(dataUrl); renderPreview(prefix); }
function removerFoto(prefix, idx)       { fotos[prefix].splice(idx, 1); renderPreview(prefix); }

function renderPreview(prefix) {
  document.getElementById('preview-' + prefix).innerHTML =
    fotos[prefix].map((src, i) => `
      <div class="photo-thumb" onclick="abrirLightbox(fotos['${prefix}'],${i})">
        <img src="${src}">
        <button class="remove-photo" onclick="event.stopPropagation();removerFoto('${prefix}',${i})">✕</button>
      </div>`).join('');
}

    /* ══ lightbox.js ══ */
/* lightbox.js — Visualizador de fotos */

let lbFotos = [], lbIdx = 0;

function abrirLightbox(arr, idx) {
  lbFotos = arr; lbIdx = idx;
  document.getElementById('lightbox-img').src = lbFotos[lbIdx];
  document.getElementById('lightbox').classList.add('open');
}

function abrirLightboxReg(id, idx) {
  const reg = window._registros.find(r => r._id === id);
  if (reg && reg.fotos) abrirLightbox(reg.fotos, idx);
}

function fecharLightbox(e) {
  const lb = document.getElementById('lightbox');
  if (!e || e.target === lb || e.target.tagName !== 'IMG') lb.classList.remove('open');
}

function lightboxNav(dir) {
  lbIdx = (lbIdx + dir + lbFotos.length) % lbFotos.length;
  document.getElementById('lightbox-img').src = lbFotos[lbIdx];
}

document.addEventListener('keydown', e => {
  const lb = document.getElementById('lightbox');
  if (!lb.classList.contains('open')) return;
  if (e.key === 'Escape')     fecharLightbox();
  if (e.key === 'ArrowLeft')  lightboxNav(-1);
  if (e.key === 'ArrowRight') lightboxNav(1);
});

    /* ══ registros.js ══ */
/* registros.js — Salvar, listar, filtrar e excluir registros */

window._registros = [];
let filtroAtual = 'todos', buscaAtual = '';

function iniciarListener() {
  const ref = window._query(window._col(window._db, 'registros'), window._orderBy('timestamp', 'desc'));
  window._onSnapshot(ref, snap => {
    window._registros = snap.docs.map(d => ({ ...d.data(), _id: d.id }));
    atualizarContadores();
    if (document.getElementById('panel-historico').classList.contains('active')) renderTabela();
    renderAdminStats();
  });
}

async function registrar(tipo) {
  const usuario = getUsuario();
  if (!usuario) return;

  const pm  = { devolucao: 'dev', troca: 'troca', venda: 'venda' };
  const prefix = pm[tipo];
  const btn = document.getElementById('btn-' + prefix);
  const lbls = { devolucao: '↩ Registrar Devolução', troca: '⇄ Registrar Troca', venda: '⚠ Registrar Venda Errada' };

  btn.disabled = true; btn.textContent = 'Salvando...';

  const reg = {
    tipo, timestamp: Date.now(),
    data: new Date().toLocaleDateString('pt-BR'),
    hora: new Date().toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' }),
    autorUid:  usuario._uid,
    autorNome: usuario.nome,
    autorEmail: usuario.email,
    fotos: [...fotos[prefix]]
  };

  if (tipo === 'devolucao') {
    reg.sku = document.getElementById('dev-sku').value.trim();
    reg.produto = document.getElementById('dev-nome').value.trim();
    reg.qtd = document.getElementById('dev-qtd').value;
    reg.cliente = document.getElementById('dev-cliente').value.trim();
    reg.motivo = document.getElementById('dev-motivo').value;
    reg.status = document.getElementById('dev-status').value;
    reg.obs = document.getElementById('dev-obs').value.trim();
    if (!reg.produto) { showToast('Informe o nome do produto.', 'error'); btn.disabled=false; btn.textContent=lbls[tipo]; return; }
    ['dev-sku','dev-nome','dev-cliente','dev-obs'].forEach(id => document.getElementById(id).value = '');
    document.getElementById('dev-qtd').value='1'; document.getElementById('dev-motivo').value=''; document.getElementById('dev-status').value='pendente';
  }

  if (tipo === 'troca') {
    reg.sku = document.getElementById('troca-sku-orig').value.trim();
    reg.produto = document.getElementById('troca-nome-orig').value.trim() + ' → ' + document.getElementById('troca-nome-sub').value.trim();
    reg.qtd = document.getElementById('troca-qtd').value;
    reg.cliente = document.getElementById('troca-cliente').value.trim();
    reg.motivo = document.getElementById('troca-motivo').value;
    reg.status = document.getElementById('troca-status').value;
    reg.obs = document.getElementById('troca-obs').value.trim();
    if (!document.getElementById('troca-nome-orig').value.trim()) { showToast('Informe o produto original.', 'error'); btn.disabled=false; btn.textContent=lbls[tipo]; return; }
    ['troca-sku-orig','troca-nome-orig','troca-sku-sub','troca-nome-sub','troca-cliente','troca-obs'].forEach(id => document.getElementById(id).value = '');
    document.getElementById('troca-qtd').value='1'; document.getElementById('troca-motivo').value=''; document.getElementById('troca-status').value='pendente';
  }

  if (tipo === 'venda') {
    reg.sku = document.getElementById('venda-sku').value.trim();
    reg.produto = document.getElementById('venda-nome').value.trim();
    reg.qtd = document.getElementById('venda-qtd').value;
    reg.cliente = document.getElementById('venda-cliente').value.trim();
    reg.motivo = document.getElementById('venda-tipo').value;
    reg.status = document.getElementById('venda-status').value;
    reg.obs = document.getElementById('venda-obs').value.trim();
    reg.pedido = document.getElementById('venda-pedido').value.trim();
    if (!reg.produto) { showToast('Informe o nome do produto.', 'error'); btn.disabled=false; btn.textContent=lbls[tipo]; return; }
    ['venda-sku','venda-nome','venda-cliente','venda-obs','venda-pedido'].forEach(id => document.getElementById(id).value = '');
    document.getElementById('venda-qtd').value='1'; document.getElementById('venda-tipo').value=''; document.getElementById('venda-status').value='pendente';
  }

  try {
    await window._addDoc(window._col(window._db, 'registros'), reg);
    fotos[prefix] = []; renderPreview(prefix); fecharCamera(prefix);
    showToast('✓ Salvo!', 'success');
  } catch (e) {
    showToast('Erro ao salvar.', 'error'); console.error(e);
  }
  btn.disabled = false; btn.textContent = lbls[tipo];
}

async function deletar(id) {
  if (!confirm('Excluir este registro?')) return;
  try { await window._deleteDoc(window._doc(window._db, 'registros', id)); showToast('Excluído.', 'success'); }
  catch (e) { showToast('Erro ao excluir.', 'error'); }
}

function atualizarContadores() {
  document.getElementById('count-dev').textContent   = window._registros.filter(r => r.tipo === 'devolucao').length;
  document.getElementById('count-troca').textContent = window._registros.filter(r => r.tipo === 'troca').length;
  document.getElementById('count-venda').textContent = window._registros.filter(r => r.tipo === 'venda').length;
}

function renderTabela() {
  const body = document.getElementById('tabela-body');
  const empty = document.getElementById('empty-state');
  let dados = window._registros.filter(r => filtroAtual === 'todos' || r.tipo === filtroAtual);
  if (buscaAtual) {
    const q = buscaAtual.toLowerCase();
    dados = dados.filter(r => (r.produto||'').toLowerCase().includes(q)||(r.cliente||'').toLowerCase().includes(q)||(r.autorNome||'').toLowerCase().includes(q)||(r.sku||'').toLowerCase().includes(q));
  }
  if (!dados.length) { body.innerHTML = ''; empty.style.display = 'block'; return; }
  empty.style.display = 'none';

  const bc = { devolucao:'badge-dev', troca:'badge-troca', venda:'badge-venda' };
  const bl = { devolucao:'↩ Dev',     troca:'⇄ Troca',    venda:'⚠ Venda' };
  const sc = { pendente:'s-pendente', andamento:'s-andamento', resolvido:'s-resolvido' };
  const sl = { pendente:'Pendente',   andamento:'Em Andamento', resolvido:'Resolvido' };

  body.innerHTML = dados.map((r, i) => {
    const fotosHtml = (r.fotos||[]).length
      ? `<div class="table-photos">${(r.fotos||[]).map((src,fi)=>`<img class="table-photo-thumb" src="${src}" onclick="abrirLightboxReg('${r._id}',${fi})">`).join('')}</div>`
      : `<span class="no-photo">—</span>`;
    const cor = corNome(r.autorNome||'');
    return `<tr>
      <td style="color:var(--muted)">${dados.length-i}</td>
      <td><span class="badge ${bc[r.tipo]||''}">${bl[r.tipo]||r.tipo}</span></td>
      <td><strong>${r.produto||'—'}</strong>${r.sku?`<br><span style="color:var(--muted);font-size:11px">${r.sku}</span>`:''}</td>
      <td>${r.cliente||'—'}</td>
      <td>${r.qtd||1}</td>
      <td style="max-width:140px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">${r.motivo||'—'}</td>
      <td>${fotosHtml}</td>
      <td><div class="author-chip"><div class="author-dot" style="background:${cor}">${iniciais(r.autorNome)}</div><span>${r.autorNome||'—'}</span></div></td>
      <td>
        <select class="status-select ${sc[r.status]||'s-pendente'}" onchange="atualizarStatus('${r._id}', this.value)" style="background:var(--surface2);border:1px solid var(--border);border-radius:var(--radius-sm);color:var(--text);font-family:var(--font-head);font-size:11px;font-weight:700;padding:4px 8px;cursor:pointer;outline:none;">
          <option value="pendente"  ${r.status==='pendente'  ?'selected':''}>⏳ Pendente</option>
          <option value="andamento" ${r.status==='andamento' ?'selected':''}>🔄 Em Andamento</option>
          <option value="resolvido" ${r.status==='resolvido' ?'selected':''}>✅ Resolvido</option>
        </select>
      </td>
      <td style="color:var(--muted);white-space:nowrap;font-size:12px">${r.data||''} ${r.hora||''}</td>
      <td style="display:flex;gap:6px;align-items:center">
        <button class="btn-comments ${(r._comentarios||0)>0?'has-comments':''}" onclick="abrirComentarios('${r._id}')">💬${(r._comentarios||0)>0?' '+r._comentarios:''}</button>
        ${getUsuario()?.role==='admin'?`<button class="btn-delete" onclick="deletar('${r._id}')">✕</button>`:''}
      </td>
    </tr>`;
  }).join('');
}

async function atualizarStatus(id, novoStatus) {
  try {
    await window._updateDoc(window._doc(window._db, 'registros', id), { status: novoStatus });
    showToast('✓ Status atualizado!', 'success');
  } catch(e) { showToast('Erro ao atualizar status.', 'error'); }
}

function filtrarTabela(val) { buscaAtual = val; renderTabela(); }
function setFiltro(tipo, btn) {
  filtroAtual = tipo;
  document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active-filter'));
  btn.classList.add('active-filter');
  renderTabela();
}

    /* ══ admin.js ══ */
/* ═══════════════════════════════════════════
   admin.js
   Gerencia funcionários usando Firebase Auth
   para criar/excluir contas + Firestore para perfis.

   COMO FUNCIONA:
   - Cada funcionário tem uma conta no Firebase Auth (e-mail + senha segura)
   - O perfil (nome, cargo, role) fica no Firestore na coleção "funcionarios"
   - O documento do Firestore usa a UID do Firebase Auth como ID
   - As senhas NUNCA ficam no Firestore
═══════════════════════════════════════════ */

window._registros    = [];
window._funcionarios = [];

// ── CARREGAR PERFIS ──────────────────────────

/**
 * Carrega a lista de perfis da coleção "funcionarios".
 * Popula a tela de login e o painel Admin.
 */
async function carregarFuncionarios() {
  const snap = await window._getDocs(window._col(window._db, 'funcionarios'));
  window._funcionarios = snap.docs.map(d => ({ ...d.data(), _uid: d.id }));
}

// ── STATS ────────────────────────────────────

function renderAdminStats() {
  document.getElementById('adm-total').textContent     = window._registros.length;
  document.getElementById('adm-pendentes').textContent = window._registros.filter(r => r.status === 'pendente').length;
  document.getElementById('adm-funcs').textContent     = window._funcionarios.length;
}

// ── LISTA DE FUNCIONÁRIOS ────────────────────

function renderAdmin() {
  renderAdminStats();
  document.getElementById('func-list').innerHTML =
    window._funcionarios.map(f => {
      const cor = corNome(f.nome);
      const qtd = window._registros.filter(r => r.autorUid === f._uid).length;
      return `
        <div class="func-item">
          <div class="func-item-left">
            <div class="func-avatar" style="background:${cor}">${iniciais(f.nome)}</div>
            <div class="func-info">
              <div class="func-name">${f.nome}</div>
              <div class="func-meta">${f.email} · ${qtd} registro${qtd !== 1 ? 's' : ''}</div>
            </div>
          </div>
          <div style="display:flex;align-items:center;gap:8px">
            <span class="role-badge ${f.role === 'admin' ? 'role-admin' : 'role-func'}">
              ${f.role === 'admin' ? 'Admin' : 'Func.'}
            </span>
            ${f._uid !== getUsuario()?._uid
              ? `<button class="btn-func-del" onclick="removerFuncionario('${f._uid}')">✕</button>`
              : '<span style="font-size:11px;color:var(--muted)">(você)</span>'}
          </div>
        </div>`;
    }).join('');
}

// ── ADICIONAR FUNCIONÁRIO ────────────────────

/**
 * Cria uma conta no Firebase Auth e salva o perfil no Firestore.
 * O administrador informa: nome, e-mail, senha e cargo.
 * A senha é enviada diretamente ao Firebase — nunca fica salva aqui.
 */
async function adicionarFuncionario() {
  const nome  = document.getElementById('nf-nome').value.trim();
  const email = document.getElementById('nf-email').value.trim().toLowerCase();
  const senha = document.getElementById('nf-senha').value;
  const role  = document.getElementById('nf-role').value;

  if (!nome || !email || !senha) { showToast('Preencha todos os campos.', 'error'); return; }
  if (senha.length < 6)          { showToast('Senha deve ter no mínimo 6 caracteres.', 'error'); return; }

  try {
    // 1. Cria conta no Firebase Authentication
    const cred = await window._createUserWithEmail(window._auth, email, senha);

    // 2. Salva perfil no Firestore (UID como ID do documento)
    await window._setDoc(window._doc(window._db, 'funcionarios', cred.user.uid), {
      nome, email, role,
      criadoEm: new Date().toISOString()
    });

    // 3. Atualiza lista local
    await carregarFuncionarios();
    renderAdmin();

    // 4. Limpa formulário
    ['nf-nome', 'nf-email', 'nf-senha'].forEach(id => document.getElementById(id).value = '');
    showToast(`✓ ${nome} adicionado!`, 'success');

  } catch (e) {
    let msg = 'Erro ao adicionar funcionário.';
    if (e.code === 'auth/email-already-in-use') msg = 'Este e-mail já está cadastrado.';
    if (e.code === 'auth/invalid-email')        msg = 'E-mail inválido.';
    if (e.code === 'auth/weak-password')        msg = 'Senha muito fraca (mín. 6 caracteres).';
    showToast(msg, 'error');
  }
}

// ── REMOVER FUNCIONÁRIO ──────────────────────

/**
 * Remove apenas o perfil do Firestore.
 * Nota: remover a conta do Firebase Auth requer o Admin SDK (backend).
 * O funcionário não conseguirá mais acessar o sistema pois o perfil
 * não existirá, mesmo que a conta Auth permaneça.
 */
async function removerFuncionario(uid) {
  if (!confirm('Remover este funcionário? Ele não conseguirá mais acessar o sistema.')) return;
  try {
    await window._deleteDoc(window._doc(window._db, 'funcionarios', uid));
    await carregarFuncionarios();
    renderAdmin();
    showToast('Funcionário removido.', 'success');
  } catch (e) {
    showToast('Erro ao remover.', 'error');
  }
}

    /* ══ app.js ══ */
/* app.js — Inicialização e navegação */

document.addEventListener('firebase-ready', async () => {
  await carregarFuncionarios();
  iniciarListener();

  window._onAuthStateChanged(window._auth, async (firebaseUser) => {
    document.getElementById('loading-screen').style.display = 'none';

    if (firebaseUser) {
      try {
        const snap = await window._getDoc(window._doc(window._db, 'funcionarios', firebaseUser.uid));
        if (snap.exists()) {
          usuarioLogado = { ...snap.data(), _uid: firebaseUser.uid };
          document.getElementById('login-screen').classList.remove('visible');
          document.getElementById('app').classList.add('visible');
          document.getElementById('panel-dashboard').classList.add('active');
          renderDashboard();
          const cor = corNome(usuarioLogado.nome);
          const av  = document.getElementById('user-avatar-hdr');
          av.style.background = cor; av.textContent = iniciais(usuarioLogado.nome);
          document.getElementById('user-name-hdr').textContent = usuarioLogado.nome;
          document.getElementById('user-role-hdr').textContent = usuarioLogado.role === 'admin' ? 'Administrador' : 'Funcionário';
          document.querySelector('.tab-admin').style.display = usuarioLogado.role === 'admin' ? 'flex' : 'none';
          return;
        }
      } catch(e) { console.error(e); }
    }
    document.getElementById('login-screen').classList.add('visible');
  });
});

document.querySelectorAll('.tab-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    const tab = btn.dataset.tab;
    document.querySelectorAll('.form-panel, #panel-historico, #panel-admin, #panel-dashboard').forEach(p => p.classList.remove('active'));
    if (tab === 'historico') { document.getElementById('panel-historico').classList.add('active'); renderTabela(); }
    else if (tab === 'dashboard') { document.getElementById('panel-dashboard').classList.add('active'); renderDashboard(); }
    else if (tab === 'admin') { document.getElementById('panel-admin').classList.add('active'); renderAdmin(); }
    else document.getElementById('panel-' + tab).classList.add('active');
  });
});



    /* ══ dashboard.js ══ */
    let periodoAtual = 'semana';
    let chartLinha = null, chartDonut = null, chartStatus = null;

    function setPeriodo(p, btn) {
      periodoAtual = p;
      document.querySelectorAll('.dash-period-btn').forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
      renderDashboard();
    }

    function filtrarPorPeriodo(dados) {
      const agora = Date.now();
      if (periodoAtual === 'semana') return dados.filter(r => r.timestamp >= agora - 7*24*60*60*1000);
      if (periodoAtual === 'mes')    return dados.filter(r => r.timestamp >= agora - 30*24*60*60*1000);
      return dados;
    }

    function renderDashboard() {
      if (!document.getElementById('panel-dashboard') || !document.getElementById('panel-dashboard').classList.contains('active')) return;
      const dados  = filtrarPorPeriodo(window._registros);
      const devs   = dados.filter(r => r.tipo === 'devolucao');
      const trocas = dados.filter(r => r.tipo === 'troca');
      const vendas = dados.filter(r => r.tipo === 'venda');
      const pend   = dados.filter(r => r.status === 'pendente');

      document.getElementById('kpi-dev').textContent       = devs.length;
      document.getElementById('kpi-troca').textContent     = trocas.length;
      document.getElementById('kpi-venda').textContent     = vendas.length;
      document.getElementById('kpi-pendentes').textContent = pend.length;

      const agora = Date.now();
      const duracao = periodoAtual === 'semana' ? 7*24*60*60*1000 : periodoAtual === 'mes' ? 30*24*60*60*1000 : null;
      if (duracao) {
        const anterior = window._registros.filter(r => r.timestamp >= agora - 2*duracao && r.timestamp < agora - duracao);
        const setTrend = (elId, atual, tipo) => {
          const antCount = anterior.filter(r => r.tipo === tipo).length;
          const el = document.getElementById(elId);
          if (!antCount) { el.textContent = ''; return; }
          const diff = atual - antCount;
          const pct  = Math.round(Math.abs(diff / antCount) * 100);
          el.textContent = diff > 0 ? `▲ ${pct}% vs período anterior` : diff < 0 ? `▼ ${pct}% vs período anterior` : '= mesmo período anterior';
          el.className = 'kpi-trend ' + (diff > 0 ? 'up' : diff < 0 ? 'down' : '');
        };
        setTrend('kpi-dev-trend',   devs.length,   'devolucao');
        setTrend('kpi-troca-trend', trocas.length, 'troca');
        setTrend('kpi-venda-trend', vendas.length, 'venda');
      }

      const dias = periodoAtual === 'semana' ? 7 : 30;
      const labels = [], devD = [], trocaD = [], vendaD = [];
      for (let i = dias-1; i >= 0; i--) {
        const d = new Date(); d.setDate(d.getDate()-i);
        const dStr = d.toLocaleDateString('pt-BR');
        labels.push(d.toLocaleDateString('pt-BR',{day:'2-digit',month:'2-digit'}));
        devD.push(dados.filter(r => r.tipo==='devolucao' && r.data===dStr).length);
        trocaD.push(dados.filter(r => r.tipo==='troca' && r.data===dStr).length);
        vendaD.push(dados.filter(r => r.tipo==='venda' && r.data===dStr).length);
      }

      const ctxL = document.getElementById('chart-linha').getContext('2d');
      if (chartLinha) chartLinha.destroy();
      chartLinha = new Chart(ctxL, {
        type:'line',
        data:{ labels, datasets:[
          {label:'Devoluções',data:devD,  borderColor:'#2563a8',backgroundColor:'rgba(37,99,168,0.1)',tension:0.4,fill:true,pointRadius:3},
          {label:'Trocas',    data:trocaD,borderColor:'#4ecdc4',backgroundColor:'rgba(78,205,196,0.1)',tension:0.4,fill:true,pointRadius:3},
          {label:'V.Erradas', data:vendaD,borderColor:'#ffe66d',backgroundColor:'rgba(255,230,109,0.1)',tension:0.4,fill:true,pointRadius:3},
        ]},
        options:{responsive:true,interaction:{mode:'index',intersect:false},
          plugins:{legend:{labels:{color:'#8888a0',font:{family:'DM Mono'}}}},
          scales:{x:{ticks:{color:'#8888a0',font:{family:'DM Mono',size:11}},grid:{color:'#2e2e38'}},
                  y:{ticks:{color:'#8888a0',font:{family:'DM Mono',size:11},stepSize:1},grid:{color:'#2e2e38'},beginAtZero:true}}}
      });

      const ctxD = document.getElementById('chart-donut').getContext('2d');
      if (chartDonut) chartDonut.destroy();
      chartDonut = new Chart(ctxD, {
        type:'doughnut',
        data:{labels:['Devoluções','Trocas','V.Erradas'],
          datasets:[{data:[devs.length,trocas.length,vendas.length],backgroundColor:['#2563a8','#4ecdc4','#ffe66d'],borderWidth:0,hoverOffset:6}]},
        options:{responsive:true,cutout:'70%',
          plugins:{legend:{position:'bottom',labels:{color:'#8888a0',font:{family:'DM Mono'},padding:16}}}}
      });

      const resolvidos = dados.filter(r=>r.status==='resolvido').length;
      const andamento  = dados.filter(r=>r.status==='andamento').length;
      const ctxS = document.getElementById('chart-status').getContext('2d');
      if (chartStatus) chartStatus.destroy();
      chartStatus = new Chart(ctxS, {
        type:'doughnut',
        data:{labels:['Resolvido','Em Andamento','Pendente'],
          datasets:[{data:[resolvidos,andamento,pend.length],backgroundColor:['#44ffaa','#4ecdc4','#ffe66d'],borderWidth:0,hoverOffset:6}]},
        options:{responsive:true,cutout:'70%',
          plugins:{legend:{position:'bottom',labels:{color:'#8888a0',font:{family:'DM Mono'},padding:16}}}}
      });

      const contagem = {};
      dados.forEach(r => {
        const nome = (r.produto||'').split('→')[0].trim() || 'Sem nome';
        contagem[nome] = (contagem[nome]||0) + 1;
      });
      const top = Object.entries(contagem).sort((a,b)=>b[1]-a[1]).slice(0,8);
      const maxVal = top[0]?.[1]||1;
      const cores = ['#2563a8','#4ecdc4','#ffe66d','#b57bff','#44ffaa','#54a0ff','#ff6b81','#a29bfe'];
      document.getElementById('top-produtos').innerHTML = top.length
        ? top.map(([nome,count],i)=>`
            <div class="top-produto-item">
              <div class="top-produto-rank">${i+1}</div>
              <div class="top-produto-bar-wrap"><div class="top-produto-bar" style="width:${Math.round(count/maxVal*100)}%;background:${cores[i%cores.length]}"></div></div>
              <div class="top-produto-nome" title="${nome}">${nome}</div>
              <div class="top-produto-count">${count}</div>
            </div>`).join('')
        : '<div style="color:var(--muted);font-size:13px;text-align:center;padding:20px">Nenhum registro no período</div>';
    }

    /* ══ comments.js ══ */
    window.comentariosUnsub = null;
    let comentarioRegistroId = null;

    async function abrirComentarios(registroId) {
      comentarioRegistroId = registroId;
      const reg = window._registros.find(r => r._id === registroId);
      document.getElementById('modal-produto-nome').textContent = reg ? (reg.produto||'Registro') : 'Registro';
      document.getElementById('comment-input').value = '';
      document.getElementById('modal-comentarios').style.display = 'flex';
      carregarComentarios(registroId);
    }

    function carregarComentarios(registroId) {
      const list = document.getElementById('comment-list');
      list.innerHTML = '<div class="comment-empty">Carregando...</div>';
      if (window.comentariosUnsub) window.comentariosUnsub();

      const ref = window._query(
        window._col(window._db, 'registros', registroId, 'comentarios'),
        window._orderBy('timestamp', 'asc')
      );
      window.comentariosUnsub = window._onSnapshot(ref, snap => {
        if (snap.empty) { list.innerHTML = '<div class="comment-empty">Nenhum comentário ainda.<br>Seja o primeiro a adicionar uma anotação!</div>'; return; }
        list.innerHTML = snap.docs.map(d => {
          const c = d.data();
          const cor = corNome(c.autorNome||'');
          const data = c.timestamp ? new Date(c.timestamp).toLocaleString('pt-BR') : '';
          return `<div class="comment-item">
            <div class="comment-top">
              <div class="comment-avatar" style="background:${cor}">${iniciais(c.autorNome||'?')}</div>
              <div class="comment-meta"><div class="comment-autor">${c.autorNome||'—'}</div><div class="comment-time">${data}</div></div>
            </div>
            <div class="comment-text">${(c.texto||'').replace(/</g,'&lt;')}</div>
          </div>`;
        }).join('');
        list.scrollTop = list.scrollHeight;
        const count = snap.docs.length;
        const reg = window._registros.find(r => r._id === registroId);
        if (reg) reg._comentarios = count;
      });
    }

    async function enviarComentario() {
      const texto = document.getElementById('comment-input').value.trim();
      if (!texto) return;
      const btn = document.getElementById('btn-send-comment');
      btn.disabled = true; btn.textContent = 'Enviando...';
      try {
        await window._addDoc(
          window._col(window._db, 'registros', comentarioRegistroId, 'comentarios'),
          { texto, autorUid: getUsuario()._uid, autorNome: getUsuario().nome, autorEmail: getUsuario().email, timestamp: Date.now() }
        );
        document.getElementById('comment-input').value = '';
      } catch(e) { showToast('Erro ao enviar comentário.', 'error'); }
      finally { btn.disabled = false; btn.textContent = 'Enviar →'; }
    }

  </script>
</body>
</html>
