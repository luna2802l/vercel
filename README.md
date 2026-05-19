<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SensPulse Pro — Dashboard Clínico</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,wght@0,300;0,400;0,500;0,600;1,300&family=Syne:wght@700;800&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.2/css/all.min.css">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
/* ── VARIABLES ── */
:root {
  --bg: #06080f;
  --surface: #0d1117;
  --surface2: #151b26;
  --surface3: #1c2535;
  --border: rgba(255,255,255,.07);
  --border-md: rgba(255,255,255,.12);
  --text: #e8edf5;
  --muted: #7a8a9e;
  --dim: #3d4e62;
  --cyan: #00d4e8;
  --cyan2: #007aff;
  --rose: #f43f5e;
  --amber: #f59e0b;
  --green: #10b981;
  --violet: #8b5cf6;
  --font-display: 'Syne', sans-serif;
  --font-body: 'DM Sans', sans-serif;
  --r-sm: 10px;
  --r-md: 16px;
  --r-lg: 22px;
  --r-xl: 30px;
  --shadow: 0 20px 60px rgba(0,0,0,.5);
  --shadow-sm: 0 4px 20px rgba(0,0,0,.3);
  --transition: .22s cubic-bezier(.4,0,.2,1);
}

/* ── RESET ── */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
html { scroll-behavior: smooth; }
body {
  font-family: var(--font-body);
  background: var(--bg);
  color: var(--text);
  min-height: 100vh;
  overflow-x: hidden;
  font-size: 15px;
  line-height: 1.6;
}
button, input, textarea, select { font-family: inherit; font-size: inherit; }
button { cursor: pointer; border: none; background: none; }
input, textarea, select { outline: none; }
a { color: inherit; text-decoration: none; }

/* ── SCROLLBAR ── */
::-webkit-scrollbar { width: 4px; height: 4px; }
::-webkit-scrollbar-track { background: transparent; }
::-webkit-scrollbar-thumb { background: var(--dim); border-radius: 99px; }

/* ── BACKGROUND MESH ── */
.bg-mesh {
  position: fixed; inset: 0; pointer-events: none; z-index: 0;
  background:
    radial-gradient(ellipse 60% 40% at 20% 0%, rgba(0,212,232,.06) 0%, transparent 60%),
    radial-gradient(ellipse 50% 40% at 85% 10%, rgba(139,92,246,.07) 0%, transparent 55%),
    radial-gradient(ellipse 40% 50% at 5% 80%, rgba(16,185,129,.05) 0%, transparent 55%),
    radial-gradient(ellipse 55% 45% at 90% 85%, rgba(244,63,94,.05) 0%, transparent 55%);
}

/* ── LAYOUT ── */
.app { position: relative; z-index: 1; display: flex; min-height: 100vh; }

/* ── SIDEBAR ── */
.sidebar {
  width: 72px;
  position: fixed; left: 0; top: 0; bottom: 0; z-index: 40;
  background: var(--surface);
  border-right: 1px solid var(--border);
  display: flex; flex-direction: column; align-items: center;
  padding: 20px 0; gap: 6px;
  transition: width var(--transition);
}
.sidebar:hover { width: 200px; }
.sidebar-logo {
  width: 40px; height: 40px; border-radius: var(--r-sm);
  background: linear-gradient(135deg, var(--cyan), var(--violet));
  display: grid; place-items: center;
  font-size: 1rem; color: #fff;
  flex-shrink: 0; margin-bottom: 16px;
}
.sidebar-nav { display: flex; flex-direction: column; gap: 4px; width: 100%; padding: 0 10px; }
.s-link {
  display: flex; align-items: center; gap: 12px;
  padding: 11px 11px; border-radius: var(--r-sm);
  color: var(--muted); font-weight: 500; font-size: .88rem;
  white-space: nowrap; overflow: hidden;
  transition: background var(--transition), color var(--transition);
  border: 1px solid transparent;
}
.s-link i { width: 18px; text-align: center; flex-shrink: 0; font-size: .95rem; }
.s-link span { opacity: 0; transition: opacity .15s; pointer-events: none; }
.sidebar:hover .s-link span { opacity: 1; }
.s-link:hover { background: var(--surface2); color: var(--text); }
.s-link.active {
  background: rgba(0,212,232,.1); color: var(--cyan);
  border-color: rgba(0,212,232,.2);
}
.sidebar-bottom { margin-top: auto; width: 100%; padding: 0 10px; display: flex; flex-direction: column; gap: 4px; }
.sidebar-user {
  display: flex; align-items: center; gap: 10px;
  padding: 11px 11px; border-radius: var(--r-sm);
  background: var(--surface2); overflow: hidden;
  white-space: nowrap;
}
.s-avatar {
  width: 28px; height: 28px; border-radius: 50%;
  background: linear-gradient(135deg, var(--cyan2), var(--violet));
  display: grid; place-items: center; font-size: .7rem; color: #fff;
  flex-shrink: 0;
}
.sidebar-user .s-name { font-size: .82rem; font-weight: 600; opacity: 0; transition: opacity .15s; }
.sidebar:hover .s-name { opacity: 1; }

/* ── MAIN ── */
.main { flex: 1; margin-left: 72px; display: flex; flex-direction: column; }

/* ── TOPBAR ── */
.topbar {
  position: sticky; top: 0; z-index: 30;
  display: flex; align-items: center; justify-content: space-between;
  padding: 0 28px; height: 60px;
  background: rgba(6,8,15,.85);
  backdrop-filter: blur(20px);
  border-bottom: 1px solid var(--border);
  gap: 16px;
}
.topbar-left { display: flex; align-items: center; gap: 14px; }
.page-title { font-family: var(--font-display); font-size: 1rem; letter-spacing: -.01em; }
.breadcrumb { font-size: .8rem; color: var(--muted); }
.topbar-right { display: flex; align-items: center; gap: 10px; }
.topbar-badge {
  display: flex; align-items: center; gap: 7px;
  padding: 6px 12px; border-radius: var(--r-sm);
  background: var(--surface2); border: 1px solid var(--border);
  font-size: .8rem; color: var(--muted);
  transition: border-color var(--transition);
}
.topbar-badge:hover { border-color: var(--border-md); }
.topbar-badge i { font-size: .75rem; }
.notif-dot {
  width: 7px; height: 7px; border-radius: 50%;
  background: var(--rose); flex-shrink: 0;
}
.live-dot {
  width: 7px; height: 7px; border-radius: 50%;
  background: var(--green); flex-shrink: 0;
  box-shadow: 0 0 0 0 rgba(16,185,129,.6);
  animation: ping 2s infinite;
}
@keyframes ping {
  0%,100% { box-shadow: 0 0 0 0 rgba(16,185,129,.6); }
  50% { box-shadow: 0 0 0 6px transparent; }
}
.btn-icon {
  width: 36px; height: 36px; border-radius: var(--r-sm);
  display: grid; place-items: center;
  background: var(--surface2); border: 1px solid var(--border);
  color: var(--muted); font-size: .85rem;
  transition: all var(--transition);
}
.btn-icon:hover { background: var(--surface3); color: var(--text); border-color: var(--border-md); }

/* ── NOTIFICATION PANEL ── */
.notif-panel {
  position: fixed; top: 66px; right: 16px; width: 340px; z-index: 50;
  background: var(--surface); border: 1px solid var(--border-md);
  border-radius: var(--r-lg); padding: 16px;
  box-shadow: var(--shadow);
  opacity: 0; transform: translateY(-8px) scale(.97);
  pointer-events: none; transition: all .2s cubic-bezier(.4,0,.2,1);
}
.notif-panel.open { opacity: 1; transform: none; pointer-events: auto; }
.notif-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 12px; }
.notif-header h3 { font-family: var(--font-display); font-size: .95rem; }
.notif-clear { font-size: .78rem; color: var(--muted); padding: 4px 8px; border-radius: 6px; transition: color var(--transition); }
.notif-clear:hover { color: var(--rose); }
.notif-list { display: flex; flex-direction: column; gap: 8px; max-height: 320px; overflow-y: auto; }
.notif-item {
  padding: 12px 14px; border-radius: var(--r-sm);
  background: var(--surface2); border: 1px solid var(--border);
}
.notif-item strong { display: block; font-size: .88rem; margin-bottom: 3px; }
.notif-item p { font-size: .8rem; color: var(--muted); line-height: 1.5; }
.notif-item small { font-size: .74rem; color: var(--dim); }

/* ── CONTENT ── */
.content { flex: 1; padding: 28px; display: flex; flex-direction: column; gap: 28px; }

/* ── SCREENS ── */
.screen { display: none; }
.screen.active { display: flex; flex-direction: column; gap: 24px; animation: fadeUp .3s ease both; }
@keyframes fadeUp { from { opacity: 0; transform: translateY(12px); } to { opacity: 1; transform: none; } }

/* ── CARDS / PANELS ── */
.card {
  background: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--r-lg);
  padding: 22px;
}
.card-sm { padding: 16px; }
.card-header {
  display: flex; align-items: flex-start; justify-content: space-between;
  margin-bottom: 18px; gap: 12px;
}
.card-title { font-family: var(--font-display); font-size: 1.05rem; }
.card-subtitle { font-size: .8rem; color: var(--muted); margin-top: 2px; }
.tag {
  display: inline-flex; align-items: center; gap: 5px;
  padding: 4px 10px; border-radius: 99px;
  font-size: .75rem; font-weight: 600; border: 1px solid;
}
.tag-cyan { background: rgba(0,212,232,.1); color: var(--cyan); border-color: rgba(0,212,232,.2); }
.tag-rose { background: rgba(244,63,94,.1); color: var(--rose); border-color: rgba(244,63,94,.2); }
.tag-amber { background: rgba(245,158,11,.1); color: var(--amber); border-color: rgba(245,158,11,.2); }
.tag-green { background: rgba(16,185,129,.1); color: var(--green); border-color: rgba(16,185,129,.2); }
.tag-violet { background: rgba(139,92,246,.1); color: var(--violet); border-color: rgba(139,92,246,.2); }
.tag-muted { background: var(--surface2); color: var(--muted); border-color: var(--border); }

/* ── BUTTONS ── */
.btn {
  display: inline-flex; align-items: center; justify-content: center; gap: 8px;
  padding: 10px 18px; border-radius: var(--r-sm);
  font-weight: 600; font-size: .88rem;
  transition: all var(--transition); border: 1px solid transparent;
}
.btn-primary {
  background: linear-gradient(135deg, var(--cyan2), var(--cyan));
  color: #fff; border-color: transparent;
  box-shadow: 0 4px 16px rgba(0,122,255,.25);
}
.btn-primary:hover { transform: translateY(-1px); box-shadow: 0 6px 20px rgba(0,122,255,.35); }
.btn-ghost { background: var(--surface2); color: var(--text); border-color: var(--border); }
.btn-ghost:hover { background: var(--surface3); border-color: var(--border-md); }
.btn-danger { background: rgba(244,63,94,.12); color: var(--rose); border-color: rgba(244,63,94,.2); }
.btn-danger:hover { background: rgba(244,63,94,.2); }
.btn-sm { padding: 7px 13px; font-size: .8rem; }
.btn-xs { padding: 5px 10px; font-size: .75rem; border-radius: 7px; }

/* ── FORM ── */
.field { display: flex; flex-direction: column; gap: 7px; }
.field label { font-size: .82rem; font-weight: 600; color: var(--muted); letter-spacing: .03em; text-transform: uppercase; }
.field input, .field select, .field textarea {
  background: var(--surface2); border: 1px solid var(--border);
  color: var(--text); border-radius: var(--r-sm); padding: 10px 14px;
  transition: border-color var(--transition), box-shadow var(--transition);
}
.field input:focus, .field select:focus, .field textarea:focus {
  border-color: rgba(0,212,232,.4);
  box-shadow: 0 0 0 3px rgba(0,212,232,.08);
}
.field textarea { resize: vertical; min-height: 100px; line-height: 1.6; }
.field select option { background: var(--surface2); }
.field input::placeholder, .field textarea::placeholder { color: var(--dim); }

/* ── GRID UTILS ── */
.grid-2 { display: grid; grid-template-columns: repeat(2, 1fr); gap: 16px; }
.grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; }
.grid-4 { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; }
.col-span-2 { grid-column: span 2; }
.col-span-3 { grid-column: span 3; }
.row { display: flex; gap: 12px; flex-wrap: wrap; align-items: center; }
.flex-1 { flex: 1; }
.mt-16 { margin-top: 16px; }
.mt-12 { margin-top: 12px; }

/* ── STAT CARDS ── */
.stat-card {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--r-lg); padding: 20px; position: relative; overflow: hidden;
  transition: border-color var(--transition), transform var(--transition);
}
.stat-card:hover { border-color: var(--border-md); transform: translateY(-2px); }
.stat-card::after {
  content: ''; position: absolute; top: 0; right: 0;
  width: 80px; height: 80px; border-radius: 50%;
  transform: translate(30%, -30%); opacity: .5;
}
.stat-card.cyan::after { background: radial-gradient(circle, rgba(0,212,232,.3), transparent); }
.stat-card.rose::after { background: radial-gradient(circle, rgba(244,63,94,.3), transparent); }
.stat-card.amber::after { background: radial-gradient(circle, rgba(245,158,11,.3), transparent); }
.stat-card.green::after { background: radial-gradient(circle, rgba(16,185,129,.3), transparent); }
.stat-label { font-size: .78rem; color: var(--muted); text-transform: uppercase; letter-spacing: .06em; margin-bottom: 8px; }
.stat-value { font-family: var(--font-display); font-size: 2rem; line-height: 1; margin-bottom: 6px; }
.stat-sub { font-size: .8rem; color: var(--muted); }

/* ── LOGIN OVERLAY ── */
.overlay {
  position: fixed; inset: 0; z-index: 90;
  display: flex; align-items: center; justify-content: center;
  padding: 24px;
  background: rgba(3,5,11,.88);
  backdrop-filter: blur(24px);
  transition: opacity .4s, visibility .4s;
}
.overlay.hidden { opacity: 0; visibility: hidden; pointer-events: none; }
.login-box {
  width: 100%; max-width: 480px;
  background: var(--surface); border: 1px solid var(--border-md);
  border-radius: 28px; padding: 36px;
  animation: fadeUp .4s ease both;
}
.login-box h2 {
  font-family: var(--font-display); font-size: 1.8rem;
  margin: 12px 0 6px; letter-spacing: -.02em;
}
.login-box p { color: var(--muted); font-size: .9rem; margin-bottom: 22px; line-height: 1.7; }
.login-logo {
  width: 52px; height: 52px; border-radius: 14px;
  background: linear-gradient(135deg, var(--cyan2), var(--cyan), var(--violet));
  display: grid; place-items: center; font-size: 1.3rem; color: #fff;
}
.demo-hint {
  background: var(--surface2); border: 1px solid var(--border);
  border-radius: var(--r-sm); padding: 12px 14px; margin-bottom: 18px;
  font-size: .82rem; color: var(--muted);
}
.demo-hint strong { color: var(--cyan); }
.auth-msg {
  display: flex; align-items: center; gap: 8px;
  padding: 10px 14px; border-radius: var(--r-sm);
  font-size: .82rem; margin-top: 12px;
  border: 1px solid var(--border); background: var(--surface2); color: var(--muted);
}
.auth-msg.success { background: rgba(16,185,129,.08); border-color: rgba(16,185,129,.2); color: var(--green); }
.auth-msg.error { background: rgba(244,63,94,.08); border-color: rgba(244,63,94,.2); color: var(--rose); }

/* ── LOADER ── */
.loader-overlay {
  position: fixed; inset: 0; z-index: 100;
  display: flex; align-items: center; justify-content: center;
  background: var(--bg);
  transition: opacity .5s, visibility .5s;
}
.loader-overlay.hidden { opacity: 0; visibility: hidden; pointer-events: none; }
.loader-box { text-align: center; width: 280px; }
.loader-icon {
  width: 64px; height: 64px; border-radius: 20px; margin: 0 auto 18px;
  background: linear-gradient(135deg, var(--cyan2), var(--cyan), var(--violet));
  display: grid; place-items: center; font-size: 1.6rem; color: #fff;
  animation: pulse 1.5s ease-in-out infinite;
}
@keyframes pulse { 0%,100% { transform: scale(1); } 50% { transform: scale(1.07); } }
.loader-box h3 { font-family: var(--font-display); font-size: 1.1rem; margin-bottom: 6px; }
.loader-box p { font-size: .82rem; color: var(--muted); margin-bottom: 20px; }
.loader-bar-track { height: 3px; background: var(--surface2); border-radius: 99px; overflow: hidden; }
.loader-bar-fill { height: 100%; background: linear-gradient(90deg, var(--cyan2), var(--cyan)); border-radius: 99px; width: 0; transition: width .4s ease; }

/* ── HOME SCREEN ── */
.hero-wrap {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--r-xl); padding: 32px;
  display: grid; grid-template-columns: 1fr 320px; gap: 28px; align-items: center;
  position: relative; overflow: hidden;
}
.hero-wrap::before {
  content: ''; position: absolute; top: -60px; right: -60px;
  width: 280px; height: 280px; border-radius: 50%;
  background: radial-gradient(circle, rgba(0,212,232,.07), transparent 70%);
}
.hero-kicker { font-size: .74rem; text-transform: uppercase; letter-spacing: .1em; color: var(--cyan); font-weight: 700; margin-bottom: 10px; }
.hero-title { font-family: var(--font-display); font-size: clamp(1.8rem, 3.5vw, 2.8rem); line-height: 1.1; margin-bottom: 14px; letter-spacing: -.02em; }
.hero-title span { color: var(--cyan); }
.hero-desc { color: var(--muted); font-size: .93rem; line-height: 1.75; max-width: 52ch; margin-bottom: 22px; }
.hero-stats { display: flex; gap: 24px; margin-bottom: 24px; }
.hero-stat strong { display: block; font-family: var(--font-display); font-size: 1.4rem; color: var(--text); }
.hero-stat small { font-size: .78rem; color: var(--muted); }
.phone-card {
  background: var(--surface2); border: 1px solid var(--border-md);
  border-radius: 28px; padding: 20px; min-height: 300px;
  display: flex; flex-direction: column; gap: 14px;
  animation: float 6s ease-in-out infinite;
}
@keyframes float { 0%,100% { transform: translateY(0); } 50% { transform: translateY(-8px); } }
.phone-metric {
  background: linear-gradient(135deg, rgba(0,212,232,.12), rgba(139,92,246,.12));
  border: 1px solid rgba(0,212,232,.15);
  border-radius: 18px; padding: 16px; text-align: center;
}
.phone-metric .big { font-family: var(--font-display); font-size: 2.2rem; color: var(--cyan); }
.phone-metric small { font-size: .78rem; color: var(--muted); }
.phone-rows { display: flex; flex-direction: column; gap: 8px; }
.phone-row {
  display: flex; align-items: center; gap: 10px;
  padding: 10px 13px; border-radius: var(--r-sm);
  background: var(--surface3); font-size: .82rem;
}
.phone-row i { color: var(--amber); width: 16px; text-align: center; }
.feature-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; }
.feature-card {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--r-lg); padding: 20px;
  transition: border-color var(--transition), transform var(--transition);
}
.feature-card:hover { border-color: var(--border-md); transform: translateY(-3px); }
.feature-icon {
  width: 40px; height: 40px; border-radius: var(--r-sm); margin-bottom: 14px;
  display: grid; place-items: center; font-size: .95rem;
}
.feature-icon.cyan { background: rgba(0,212,232,.12); color: var(--cyan); }
.feature-icon.rose { background: rgba(244,63,94,.12); color: var(--rose); }
.feature-icon.amber { background: rgba(245,158,11,.12); color: var(--amber); }
.feature-icon.green { background: rgba(16,185,129,.12); color: var(--green); }
.feature-icon.violet { background: rgba(139,92,246,.12); color: var(--violet); }
.feature-card h3 { font-size: .93rem; font-weight: 700; margin-bottom: 5px; }
.feature-card p { font-size: .8rem; color: var(--muted); line-height: 1.6; }

/* ── FORM SCREEN ── */
.chip-row { display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 16px; }
.chip {
  padding: 6px 13px; border-radius: 99px;
  font-size: .8rem; font-weight: 600;
  background: var(--surface2); border: 1px solid var(--border); color: var(--muted);
  transition: all var(--transition);
}
.chip:hover, .chip.active { background: rgba(0,212,232,.12); color: var(--cyan); border-color: rgba(0,212,232,.25); }
.progress-bar { display: flex; gap: 6px; margin-bottom: 20px; }
.prog-step {
  flex: 1; height: 4px; border-radius: 99px; background: var(--surface3);
  transition: background .3s;
}
.prog-step.done { background: linear-gradient(90deg, var(--cyan2), var(--cyan)); }
.prog-step.active { background: rgba(0,212,232,.3); }

/* ── DASHBOARD SCREEN ── */
.dash-grid { display: grid; grid-template-columns: 1fr 380px; gap: 20px; align-items: start; }
.result-box {
  border: 1px solid var(--border); border-radius: var(--r-lg);
  padding: 20px; display: flex; gap: 14px; align-items: flex-start;
  background: var(--surface2); min-height: 120px;
  transition: background .3s, border-color .3s;
}
.result-icon-wrap {
  width: 46px; height: 46px; border-radius: var(--r-sm); flex-shrink: 0;
  display: grid; place-items: center; font-size: 1.1rem;
  background: linear-gradient(135deg, rgba(0,212,232,.2), rgba(139,92,246,.15));
  color: var(--cyan);
}
.result-box h3 { font-family: var(--font-display); font-size: 1rem; margin-bottom: 5px; }
.result-box p { font-size: .85rem; color: var(--muted); line-height: 1.7; }
.kpi-row { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; }
.kpi {
  background: var(--surface2); border: 1px solid var(--border);
  border-radius: var(--r-sm); padding: 14px;
}
.kpi small { font-size: .74rem; color: var(--muted); text-transform: uppercase; letter-spacing: .06em; display: block; margin-bottom: 5px; }
.kpi strong { font-family: var(--font-display); font-size: 1.4rem; display: block; margin-bottom: 2px; }
.kpi span { font-size: .78rem; color: var(--muted); }
.chart-box { position: relative; height: 220px; }
.patient-summary {
  display: grid; grid-template-columns: repeat(2, 1fr); gap: 10px;
  padding: 14px; border-radius: var(--r-sm);
  background: var(--surface2); border: 1px solid var(--border);
  margin-bottom: 16px;
}
.ps-item small { font-size: .74rem; color: var(--muted); display: block; margin-bottom: 2px; }
.ps-item strong { font-size: .9rem; }

/* ── CLINICAL TABLE ── */
.table-wrap { overflow-x: auto; border-radius: var(--r-sm); border: 1px solid var(--border); }
.data-table { width: 100%; border-collapse: collapse; font-size: .85rem; }
.data-table th {
  padding: 10px 14px; text-align: left;
  font-size: .74rem; text-transform: uppercase; letter-spacing: .06em;
  color: var(--muted); background: var(--surface2);
  border-bottom: 1px solid var(--border);
}
.data-table td { padding: 12px 14px; border-bottom: 1px solid var(--border); vertical-align: middle; }
.data-table tbody tr:last-child td { border-bottom: none; }
.data-table tbody tr:hover td { background: rgba(255,255,255,.02); }
.data-table td small { display: block; font-size: .76rem; color: var(--muted); }
.priority-pill {
  display: inline-flex; align-items: center; gap: 5px;
  padding: 4px 9px; border-radius: 99px; font-size: .77rem; font-weight: 600;
}
.priority-alta { background: rgba(244,63,94,.12); color: var(--rose); border: 1px solid rgba(244,63,94,.2); }
.priority-media { background: rgba(245,158,11,.12); color: var(--amber); border: 1px solid rgba(245,158,11,.2); }
.priority-estable { background: rgba(16,185,129,.12); color: var(--green); border: 1px solid rgba(16,185,129,.2); }
.empty-state { text-align: center; padding: 32px 16px; color: var(--muted); font-size: .87rem; }
.empty-state i { font-size: 1.8rem; margin-bottom: 10px; display: block; opacity: .4; }

/* ── HISTORY SCREEN ── */
.history-filters { display: flex; gap: 10px; margin-bottom: 16px; flex-wrap: wrap; }
.history-filters .field { flex: 1; min-width: 160px; }
.history-list { display: flex; flex-direction: column; gap: 12px; }
.history-card {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: var(--r-lg); padding: 18px;
  transition: border-color var(--transition), transform var(--transition);
}
.history-card:hover { border-color: var(--border-md); transform: translateY(-2px); }
.hc-head { display: flex; justify-content: space-between; align-items: flex-start; gap: 12px; margin-bottom: 10px; }
.hc-head h3 { font-size: .95rem; font-weight: 700; margin-bottom: 2px; }
.hc-head p { font-size: .82rem; color: var(--muted); }
.hc-meta { display: flex; gap: 14px; flex-wrap: wrap; margin-bottom: 10px; }
.hc-meta span { font-size: .78rem; color: var(--muted); display: flex; align-items: center; gap: 5px; }
.hc-meta i { font-size: .72rem; }
.hc-body { font-size: .85rem; color: var(--muted); line-height: 1.65; margin-bottom: 12px; }
.hc-tags { display: flex; gap: 7px; flex-wrap: wrap; margin-bottom: 12px; }
.hc-actions { display: flex; gap: 8px; }

/* ── SUMMARY SIDEBAR ── */
.summary-panel { display: flex; flex-direction: column; gap: 14px; }
.sum-stat {
  background: var(--surface2); border: 1px solid var(--border);
  border-radius: var(--r-sm); padding: 14px;
  display: flex; align-items: center; justify-content: space-between;
}
.sum-stat .val { font-family: var(--font-display); font-size: 1.6rem; }
.sum-stat .lbl { font-size: .78rem; color: var(--muted); }
.timeline-list { display: flex; flex-direction: column; gap: 0; }
.tl-item { display: flex; gap: 12px; padding: 10px 0; border-bottom: 1px solid var(--border); }
.tl-item:last-child { border-bottom: none; }
.tl-dot { width: 8px; height: 8px; border-radius: 50%; background: var(--cyan); margin-top: 6px; flex-shrink: 0; }
.tl-item p { font-size: .82rem; line-height: 1.5; }
.tl-item small { font-size: .75rem; color: var(--dim); }

/* ── DIVIDER ── */
.divider { height: 1px; background: var(--border); margin: 6px 0; }

/* ── TOOLTIP / MISC ── */
.badge-count {
  min-width: 18px; height: 18px; padding: 0 5px; border-radius: 99px;
  background: var(--rose); color: #fff; font-size: .7rem; font-weight: 700;
  display: inline-grid; place-items: center;
}

/* ── RESPONSIVE ── */
@media (max-width: 1100px) {
  .hero-wrap { grid-template-columns: 1fr; }
  .phone-card { display: none; }
  .dash-grid { grid-template-columns: 1fr; }
  .feature-grid { grid-template-columns: repeat(2, 1fr); }
}
@media (max-width: 780px) {
  .sidebar { display: none; }
  .main { margin-left: 0; }
  .content { padding: 16px; }
  .grid-2, .grid-3, .grid-4 { grid-template-columns: 1fr; }
  .col-span-2, .col-span-3 { grid-column: auto; }
  .kpi-row { grid-template-columns: 1fr; }
  .feature-grid { grid-template-columns: 1fr; }
  .hero-stats { flex-wrap: wrap; gap: 14px; }
  .patient-summary { grid-template-columns: 1fr; }
}

/* ── PRINT ── */
@media print {
  .loader-overlay, .overlay, .sidebar, .topbar, .bg-mesh,
  #screen-0, #screen-1, #screen-3,
  .btn-ghost, .btn-primary { display: none !important; }
  #screen-2 { display: flex !important; }
  body { background: white; color: #0f1728; }
  .card, .stat-card, .result-box, .kpi { background: white !important; border-color: #d0d8e8 !important; box-shadow: none !important; }
}
</style>
</head>
<body>

<!-- BG -->
<div class="bg-mesh"></div>

<!-- LOADER -->
<div class="loader-overlay" id="loaderOverlay">
  <div class="loader-box">
    <div class="loader-icon"><i class="fa-solid fa-heart-pulse"></i></div>
    <h3>SensPulse Pro</h3>
    <p>Inicializando módulos clínicos...</p>
    <div class="loader-bar-track"><div class="loader-bar-fill" id="loaderBar"></div></div>
  </div>
</div>

<!-- LOGIN -->
<div class="overlay" id="loginOverlay">
  <div class="login-box">
    <div class="login-logo"><i class="fa-solid fa-heart-pulse"></i></div>
    <h2>Bienvenido de nuevo</h2>
    <p>Accede al dashboard clínico. Los datos se sincronizan con MySQL (XAMPP) en tiempo real.</p>
    <div class="demo-hint">
      <strong>Credenciales demo:</strong> &nbsp;admin@senspulse.demo / senspulse2026
    </div>
    <div class="grid-2" style="gap:12px; margin-bottom:12px;">
      <div class="field"><label>Correo</label><input type="email" id="fEmail" placeholder="admin@senspulse.demo"></div>
      <div class="field"><label>Contraseña</label><input type="password" id="fPass" placeholder="senspulse2026"></div>
      <div class="field"><label>Nombre</label><input type="text" id="fName" placeholder="Ej: Doctor Luna"></div>
      <div class="field">
        <label>Rol</label>
        <select id="fRole">
          <option>Médico</option><option>Enfermería</option>
          <option>Recepción</option><option>Administrador</option>
        </select>
      </div>
    </div>
    <div class="row" style="justify-content:space-between; margin-bottom:4px;">
      <label style="display:flex;align-items:center;gap:7px;font-size:.82rem;color:var(--muted);cursor:pointer;">
        <input type="checkbox" id="fRemember" checked> Recordar sesión
      </label>
      <button class="btn btn-ghost btn-xs" id="fillDemoBtn"><i class="fa-solid fa-wand-magic-sparkles"></i> Demo</button>
    </div>
    <div class="auth-msg" id="authMsg"><i class="fa-solid fa-circle-info"></i> Usa las credenciales demo o completa el formulario.</div>
    <div class="row mt-12" style="margin-top:14px;">
      <button class="btn btn-primary flex-1" id="loginBtn"><i class="fa-solid fa-fingerprint"></i> Entrar</button>
      <button class="btn btn-ghost flex-1" id="guestBtn"><i class="fa-solid fa-user"></i> Invitado</button>
    </div>
  </div>
</div>

<!-- NOTIFICATION PANEL -->
<div class="notif-panel" id="notifPanel">
  <div class="notif-header">
    <h3>Notificaciones</h3>
    <button class="notif-clear" id="clearNotifsBtn">Limpiar</button>
  </div>
  <div class="notif-list" id="notifList"></div>
</div>

<!-- APP -->
<div class="app">
  <!-- SIDEBAR -->
  <aside class="sidebar" id="sidebar">
    <div class="sidebar-logo"><i class="fa-solid fa-heart-pulse"></i></div>
    <nav class="sidebar-nav">
      <button class="s-link active" data-screen="0"><i class="fa-solid fa-house"></i><span>Inicio</span></button>
      <button class="s-link" data-screen="1"><i class="fa-solid fa-notes-medical"></i><span>Formulario</span></button>
      <button class="s-link" data-screen="2"><i class="fa-solid fa-chart-line"></i><span>Dashboard</span></button>
      <button class="s-link" data-screen="3"><i class="fa-solid fa-clock-rotate-left"></i><span>Historial</span></button>
    </nav>
    <div class="sidebar-bottom">
      <button class="s-link" id="themeBtn"><i class="fa-solid fa-circle-half-stroke"></i><span>Tema</span></button>
      <div class="sidebar-user">
        <div class="s-avatar" id="sAvatar">G</div>
        <span class="s-name" id="sName">Invitado</span>
      </div>
    </div>
  </aside>

  <!-- MAIN -->
  <div class="main">
    <!-- TOPBAR -->
    <header class="topbar">
      <div class="topbar-left">
        <span class="page-title" id="pageTitle">Inicio</span>
        <span class="breadcrumb" id="pageCrumb">SensPulse Pro</span>
      </div>
      <div class="topbar-right">
        <div class="topbar-badge"><div class="live-dot"></div><span>Sistema activo</span></div>
        <div class="topbar-badge" id="dbStatusBadge"><i class="fa-solid fa-database"></i> <span id="dbStatusText">Conectando BD...</span></div>
        <div class="topbar-badge" id="userBadge"><i class="fa-solid fa-user-doctor"></i> Invitado</div>
        <button class="btn-icon" id="notifBtn" title="Notificaciones"><i class="fa-solid fa-bell"></i></button>
        <button class="btn-icon" id="exportBtn" title="Exportar PDF"><i class="fa-solid fa-file-pdf"></i></button>
        <button class="btn-icon" id="logoutBtn" title="Cerrar sesión"><i class="fa-solid fa-right-from-bracket"></i></button>
      </div>
    </header>

    <!-- CONTENT -->
    <div class="content">

      <!-- ══ SCREEN 0: HOME ══ -->
      <div class="screen active" id="screen-0">
        <div class="hero-wrap">
          <div>
            <div class="hero-kicker"><i class="fa-solid fa-bolt"></i>&nbsp; Plataforma Clínica v2.0</div>
            <h1 class="hero-title">Orientación <span>inteligente</span><br>en salud</h1>
            <p class="hero-desc">Dashboard médico con análisis de síntomas, historial persistente local, gráficas dinámicas y exportación a PDF. Diseñado para equipos clínicos modernos.</p>
            <div class="hero-stats">
              <div class="hero-stat"><strong id="hsCases">0</strong><small>casos guardados</small></div>
              <div class="hero-stat"><strong>4</strong><small>pantallas</small></div>
              <div class="hero-stat"><strong id="hsHigh">0</strong><small>prioridad alta</small></div>
            </div>
            <div class="row">
              <button class="btn btn-primary" data-go="1"><i class="fa-solid fa-arrow-right"></i> Nuevo caso</button>
              <button class="btn btn-ghost" data-go="2"><i class="fa-solid fa-chart-pie"></i> Ver panel</button>
            </div>
          </div>
          <div class="phone-card">
            <div class="phone-metric">
              <div class="big">99%</div>
              <small>Interfaz premium</small>
            </div>
            <div class="phone-rows">
              <div class="phone-row"><i class="fa-solid fa-stethoscope"></i> Chequeo clínico</div>
              <div class="phone-row"><i class="fa-solid fa-wave-square"></i> Signos vitales</div>
              <div class="phone-row"><i class="fa-solid fa-chart-simple"></i> Dashboard vivo</div>
              <div class="phone-row"><i class="fa-solid fa-floppy-disk"></i> Historial local</div>
            </div>
          </div>
        </div>

        <div class="feature-grid">
          <div class="feature-card">
            <div class="feature-icon cyan"><i class="fa-solid fa-lungs"></i></div>
            <h3>Módulo respiratorio</h3>
            <p>Detección de patrones de tos, fiebre y dificultad respiratoria con orientación visual inmediata.</p>
          </div>
          <div class="feature-card">
            <div class="feature-icon violet"><i class="fa-solid fa-brain"></i></div>
            <h3>Módulo neurológico</h3>
            <p>Panel inteligente para confusión, mareo, debilidad y alertas de atención prioritaria.</p>
          </div>
          <div class="feature-card">
            <div class="feature-icon rose"><i class="fa-solid fa-heart-circle-check"></i></div>
            <h3>Módulo cardíaco</h3>
            <p>Visualización de riesgo cardiovascular y orientación para atención temprana.</p>
          </div>
          <div class="feature-card">
            <div class="feature-icon amber"><i class="fa-solid fa-floppy-disk"></i></div>
            <h3>Historial persistente</h3>
            <p>Todos los casos se guardan automáticamente en <strong>localStorage</strong> del navegador. Sin servidores externos.</p>
          </div>
          <div class="feature-card">
            <div class="feature-icon green"><i class="fa-solid fa-file-pdf"></i></div>
            <h3>Exportación PDF</h3>
            <p>Genera reportes clínicos en PDF directamente desde el dashboard con un solo clic.</p>
          </div>
          <div class="feature-card">
            <div class="feature-icon cyan"><i class="fa-solid fa-shield-heart"></i></div>
            <h3>Datos locales seguros</h3>
            <p>Todo se procesa en el navegador. Ningún dato sale de tu dispositivo.</p>
          </div>
        </div>
      </div>

      <!-- ══ SCREEN 1: FORM ══ -->
      <div class="screen" id="screen-1">
        <div class="grid-2" style="align-items:start;">
          <div class="card">
            <div class="card-header">
              <div>
                <div class="card-title">Registro del paciente</div>
                <div class="card-subtitle">Completa los datos para obtener orientación clínica</div>
              </div>
              <span class="tag tag-cyan"><div class="live-dot"></div> En vivo</span>
            </div>

            <div class="progress-bar">
              <div class="prog-step done"></div>
              <div class="prog-step done"></div>
              <div class="prog-step active"></div>
            </div>

            <div class="chip-row" id="chipRow">
              <button class="chip" data-t="fSintomas" data-v="fiebre">Fiebre</button>
              <button class="chip" data-t="fSintomas" data-v="tos">Tos</button>
              <button class="chip" data-t="fSintomas" data-v="dolor de cabeza">Cefalea</button>
              <button class="chip" data-t="fSintomas" data-v="mareo">Mareo</button>
              <button class="chip" data-t="fSintomas" data-v="dolor de pecho">Dolor pecho</button>
              <button class="chip" data-t="fSintomas" data-v="dificultad respiratoria">Disnea</button>
              <button class="chip" data-t="fRiesgos" data-v="contacto con enfermo">Contacto</button>
              <button class="chip" data-t="fRiesgos" data-v="picadura de mosquito">Mosquito</button>
            </div>

            <div class="grid-2">
              <div class="field"><label>Nombre completo</label><input type="text" id="fNombre" placeholder="Nombre del paciente"></div>
              <div class="field"><label>Edad</label><input type="number" id="fEdad" placeholder="34" min="0" max="120"></div>
              <div class="field">
                <label>Sexo</label>
                <select id="fSexo"><option>Masculino</option><option>Femenino</option><option>Otro</option></select>
              </div>
              <div class="field"><label>Temperatura (°C)</label><input type="number" id="fTemp" placeholder="38.5" step="0.1"></div>
              <div class="field col-span-2"><label>Síntomas</label><textarea id="fSintomas" placeholder="Ej: fiebre, tos, dolor de cabeza"></textarea></div>
              <div class="field col-span-2"><label>Factores de riesgo</label><textarea id="fRiesgos" placeholder="Ej: viaje reciente, contacto con enfermo"></textarea></div>
            </div>

            <div class="row mt-16">
              <button class="btn btn-primary flex-1" id="analyzeBtn"><i class="fa-solid fa-wand-magic-sparkles"></i> Analizar caso</button>
              <button class="btn btn-ghost" id="clearFormBtn"><i class="fa-solid fa-trash"></i> Limpiar</button>
            </div>
          </div>

          <div style="display:flex;flex-direction:column;gap:14px;">
            <div class="card card-sm">
              <div class="card-header" style="margin-bottom:12px;">
                <div class="card-title" style="font-size:.93rem;">Ayuda de síntomas</div>
              </div>
              <p style="font-size:.82rem;color:var(--muted);line-height:1.7;">Haz clic en los chips de síntomas para añadirlos automáticamente. Puedes escribir libremente en los campos de texto.</p>
            </div>
            <div class="card card-sm">
              <div class="card-header" style="margin-bottom:12px;">
                <div class="card-title" style="font-size:.93rem;">Recordatorio clínico</div>
              </div>
              <ul style="font-size:.82rem;color:var(--muted);line-height:1.9;padding-left:16px;">
                <li>Esta herramienta es educativa y visual</li>
                <li>No reemplaza diagnóstico profesional</li>
                <li>Los datos se guardan localmente</li>
                <li>Puedes exportar reportes en PDF</li>
              </ul>
            </div>
          </div>
        </div>
      </div>

      <!-- ══ SCREEN 2: DASHBOARD ══ -->
      <div class="screen" id="screen-2">
        <!-- KPIs -->
        <div class="grid-4">
          <div class="stat-card cyan">
            <div class="stat-label">Riesgo actual</div>
            <div class="stat-value" id="kpiRisk">28%</div>
            <div class="stat-sub">Bajo a moderado</div>
          </div>
          <div class="stat-card rose">
            <div class="stat-label">Prioridad</div>
            <div class="stat-value" id="kpiPriority" style="font-size:1.4rem;">Estable</div>
            <div class="stat-sub">Seguimiento sugerido</div>
          </div>
          <div class="stat-card amber">
            <div class="stat-label">Temperatura</div>
            <div class="stat-value" id="kpiTemp">--</div>
            <div class="stat-sub">Del formulario</div>
          </div>
          <div class="stat-card green">
            <div class="stat-label">Casos guardados</div>
            <div class="stat-value" id="kpiTotal">0</div>
            <div class="stat-sub">En localStorage</div>
          </div>
        </div>

        <div class="dash-grid">
          <div style="display:flex;flex-direction:column;gap:16px;">
            <!-- Patient Summary -->
            <div class="card card-sm">
              <div class="card-header" style="margin-bottom:14px;">
                <div>
                  <div class="card-title">Resumen del paciente</div>
                </div>
                <div class="row" style="gap:8px;">
                  <button class="btn btn-ghost btn-sm" id="saveCaseBtn"><i class="fa-solid fa-bookmark"></i> Guardar</button>
                  <button class="btn btn-ghost btn-sm" data-go="3"><i class="fa-solid fa-list"></i> Historial</button>
                </div>
              </div>
              <div class="patient-summary">
                <div class="ps-item"><small>Paciente</small><strong id="dPatient">Sin registro</strong></div>
                <div class="ps-item"><small>Edad</small><strong id="dAge">--</strong></div>
                <div class="ps-item"><small>Sexo</small><strong id="dSex">--</strong></div>
                <div class="ps-item"><small>Operador</small><strong id="dOperator">Invitado</strong></div>
              </div>
            </div>

            <!-- Result -->
            <div class="card card-sm">
              <div class="card-header" style="margin-bottom:14px;">
                <div class="card-title">Orientación clínica</div>
              </div>
              <div class="result-box" id="resultBox">
                <div class="result-icon-wrap" id="resultIconWrap"><i class="fa-solid fa-sparkles" id="resultIcon"></i></div>
                <div>
                  <h3 id="resultTitle">Esperando análisis</h3>
                  <p id="resultText">Complete el formulario para obtener una orientación visual del caso.</p>
                </div>
              </div>
              <div class="row mt-12" id="resultTags"></div>
            </div>

            <!-- KPI Row -->
            <div class="kpi-row">
              <div class="kpi"><small>Riesgo</small><strong id="kpi2Risk">28%</strong><span>Calculado</span></div>
              <div class="kpi"><small>Prioridad</small><strong id="kpi2Priority">Estable</strong><span>Nivel de atención</span></div>
              <div class="kpi"><small>Backend demo</small><strong id="kpiLatency">-- ms</strong><span>Latencia simulada</span></div>
            </div>

            <!-- Clinical Table -->
            <div class="card card-sm">
              <div class="card-header">
                <div><div class="card-title">Casos recientes</div></div>
                <button class="btn btn-ghost btn-xs" id="refreshTableBtn"><i class="fa-solid fa-rotate"></i> Actualizar</button>
              </div>
              <div class="table-wrap">
                <table class="data-table">
                  <thead><tr><th>Paciente</th><th>Prioridad</th><th>Riesgo</th><th>Estado</th></tr></thead>
                  <tbody id="clinicalBody"></tbody>
                </table>
              </div>
            </div>
          </div>

          <!-- RIGHT COLUMN -->
          <div style="display:flex;flex-direction:column;gap:16px;">
            <div class="card card-sm">
              <div class="card-header" style="margin-bottom:12px;">
                <div><div class="card-title">Distribución</div><div class="card-subtitle">Áreas de riesgo</div></div>
              </div>
              <div class="chart-box"><canvas id="riskChart"></canvas></div>
            </div>
            <div class="card card-sm">
              <div class="card-header" style="margin-bottom:12px;">
                <div><div class="card-title">Tendencia</div><div class="card-subtitle">Señales del paciente</div></div>
              </div>
              <div class="chart-box"><canvas id="trendChart"></canvas></div>
            </div>
            <div class="card card-sm">
              <div class="card-header" style="margin-bottom:12px;">
                <div class="card-title">Áreas médicas</div>
              </div>
              <div class="grid-2" style="gap:8px;">
                <div style="display:flex;align-items:center;gap:9px;padding:9px 12px;border-radius:9px;background:var(--surface2);font-size:.82rem;"><span>🦠</span> VIH</div>
                <div style="display:flex;align-items:center;gap:9px;padding:9px 12px;border-radius:9px;background:var(--surface2);font-size:.82rem;"><span>🫁</span> Tuberculosis</div>
                <div style="display:flex;align-items:center;gap:9px;padding:9px 12px;border-radius:9px;background:var(--surface2);font-size:.82rem;"><span>🦟</span> Malaria</div>
                <div style="display:flex;align-items:center;gap:9px;padding:9px 12px;border-radius:9px;background:var(--surface2);font-size:.82rem;"><span>💉</span> Vacunación</div>
                <div style="display:flex;align-items:center;gap:9px;padding:9px 12px;border-radius:9px;background:rgba(0,212,232,.08);border:1px solid rgba(0,212,232,.15);font-size:.82rem;"><span>❤️</span> Cardíaca</div>
                <div style="display:flex;align-items:center;gap:9px;padding:9px 12px;border-radius:9px;background:rgba(139,92,246,.08);border:1px solid rgba(139,92,246,.15);font-size:.82rem;"><span>🧠</span> Neurológica</div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- ══ SCREEN 3: HISTORY ══ -->
      <div class="screen" id="screen-3">
        <div class="grid-2" style="align-items:start;">
          <div>
            <div class="card card-sm" style="margin-bottom:16px;">
              <div class="card-header">
                <div><div class="card-title">Historial clínico</div><div class="card-subtitle">Casos guardados en localStorage</div></div>
                <div class="row" style="gap:8px;">
                  <button class="btn btn-ghost btn-sm" data-go="1"><i class="fa-solid fa-plus"></i> Nuevo</button>
                  <button class="btn btn-danger btn-sm" id="clearHistoryBtn"><i class="fa-solid fa-trash"></i> Limpiar</button>
                </div>
              </div>
              <div class="history-filters">
                <div class="field">
                  <label>Filtrar prioridad</label>
                  <select id="hFilter">
                    <option value="todos">Todos</option>
                    <option value="alta">Alta</option>
                    <option value="media">Media</option>
                    <option value="estable">Estable</option>
                  </select>
                </div>
                <div class="field">
                  <label>Buscar paciente</label>
                  <input type="text" id="hSearch" placeholder="Escribe un nombre...">
                </div>
              </div>
              <div class="history-list" id="historyList"></div>
            </div>
          </div>

          <div class="summary-panel">
            <div class="card card-sm">
              <div class="card-header" style="margin-bottom:12px;"><div class="card-title">Estadísticas</div></div>
              <div style="display:flex;flex-direction:column;gap:10px;">
                <div class="sum-stat"><span class="lbl">Total casos</span><span class="val" id="sCases">0</span></div>
                <div class="sum-stat"><span class="lbl">Prioridad alta</span><span class="val" style="color:var(--rose)" id="sHigh">0</span></div>
                <div class="sum-stat"><span class="lbl">Último paciente</span><span style="font-size:.9rem;font-weight:600;" id="sLast">--</span></div>
              </div>
            </div>

            <div class="card card-sm">
              <div class="card-header" style="margin-bottom:12px;"><div class="card-title">Características del sistema</div></div>
              <div class="timeline-list">
                <div class="tl-item"><div class="tl-dot"></div><div><p>Historial persistente con <strong>MySQL</strong> via XAMPP</p><small>Respaldo automático en localStorage</small></div></div>
                <div class="tl-item"><div class="tl-dot" style="background:var(--violet)"></div><div><p>Autenticación de sesión con memoria</p><small>Recordar usuario y rol</small></div></div>
                <div class="tl-item"><div class="tl-dot" style="background:var(--amber)"></div><div><p>Exportación de reportes a PDF</p><small>Impresión desde el dashboard</small></div></div>
                <div class="tl-item"><div class="tl-dot" style="background:var(--rose)"></div><div><p>Filtros y búsqueda en tiempo real</p><small>Por nombre y prioridad</small></div></div>
              </div>
            </div>
          </div>
        </div>
      </div>

    </div><!-- /content -->

    <footer style="padding:14px 28px;border-top:1px solid var(--border);display:flex;align-items:center;gap:10px;">
      <i class="fa-solid fa-circle-info" style="color:var(--muted);font-size:.8rem;"></i>
      <span style="font-size:.78rem;color:var(--muted);">SensPulse Pro es una herramienta educativa y visual. No reemplaza la evaluación de un profesional de la salud.</span>
    </footer>
  </div><!-- /main -->
</div><!-- /app -->

<script>
// ══════════════════════════════════════
//  STORAGE LAYER — MySQL via PHP API
//  Fallback automático a localStorage
// ══════════════════════════════════════

// Ruta de la API (misma carpeta del proyecto)
const API_BASE = 'api/';

// ── Helper fetch con timeout ───────────────────────────
async function apiFetch(path, options = {}) {
  try {
    const controller = new AbortController();
    const timer = setTimeout(() => controller.abort(), 5000);
    const res = await fetch(API_BASE + path, {
      headers: { 'Content-Type': 'application/json' },
      signal: controller.signal,
      ...options,
    });
    clearTimeout(timer);
    return await res.json();
  } catch (e) {
    return { ok: false, error: e.message };
  }
}

// Estado de conexión a MySQL
let mysqlOnline = false;
let _historyCache = null;   // caché en memoria para operaciones síncronas

// ── Verificar conexión al arrancar ─────────────────────
async function checkMySQLConnection() {
  const badge = document.getElementById('dbStatusBadge');
  const badgeTxt = document.getElementById('dbStatusText');
  const r = await apiFetch('casos.php');
  mysqlOnline = r.ok === true;
  if (mysqlOnline) {
    _historyCache = r.data || [];
    if (badge) badge.style.cssText = 'background:rgba(16,185,129,.12);border-color:rgba(16,185,129,.3);color:#10b981;';
    if (badgeTxt) badgeTxt.textContent = 'MySQL OK';
    // Si la BD está vacía, cargar datos de muestra
    if (_historyCache.length === 0) await seedIfEmpty();
    pushNotif('✅ Base de datos', 'Conectado a MySQL/phpMyAdmin. Datos sincronizados.');
  } else {
    _historyCache = DB_LOCAL.getHistory();
    if (badge) badge.style.cssText = 'background:rgba(245,158,11,.12);border-color:rgba(245,158,11,.3);color:#f59e0b;';
    if (badgeTxt) badgeTxt.textContent = 'BD: local';
    if (_historyCache.length === 0) DB.setHistory(SEED_CASES);
    pushNotif('⚠️ Sin MySQL', 'XAMPP no detectado. Usando localStorage como respaldo.');
  }
  renderHistory();
  updateHomeStats();
  refreshTable();
  return mysqlOnline;
}

// ── DB LOCAL (localStorage, respaldo) ─────────────────
const DB_LOCAL = {
  HISTORY: 'sp_history', SESSION: 'sp_session',
  THEME: 'sp_theme', NOTIFS: 'sp_notifs',
  get(key) { try { return JSON.parse(localStorage.getItem(key)||'null'); } catch { return null; } },
  set(key, val) { try { localStorage.setItem(key, JSON.stringify(val)); return true; } catch { return false; } },
  remove(key) { try { localStorage.removeItem(key); } catch {} },
  getHistory() { return this.get(this.HISTORY) || []; },
  setHistory(arr) { this.set(this.HISTORY, arr.slice(0,50)); },
  getSession() { return this.get(this.SESSION); },
  setSession(s) { this.set(this.SESSION, s); },
  clearSession() { this.remove(this.SESSION); },
  getTheme() { return this.get(this.THEME) || 'dark'; },
  getNotifs() { return this.get(this.NOTIFS) || defaultNotifications(); },
  setNotifs(n) { this.set(this.NOTIFS, n.slice(0,8)); },
  addNotif(n) { const a = this.getNotifs(); a.unshift({...n, time:'Ahora'}); this.setNotifs(a); },
};

// ── DB principal (MySQL con fallback) ─────────────────
const DB = {
  // Historial
  getHistory() {
    return _historyCache !== null ? _historyCache : DB_LOCAL.getHistory();
  },
  setHistory(arr) {
    _historyCache = arr;
    DB_LOCAL.setHistory(arr);
  },
  async addCaseAsync(c) {
    if (mysqlOnline) {
      const r = await apiFetch('casos.php', { method:'POST', body: JSON.stringify(c) });
      if (r.ok) {
        c = { ...c, id: r.id };
        if (!_historyCache) _historyCache = [];
        _historyCache.unshift(c);
        DB_LOCAL.setHistory(_historyCache);
        return _historyCache;
      }
    }
    // Fallback local
    if (!_historyCache) _historyCache = DB_LOCAL.getHistory();
    _historyCache.unshift({ ...c, id: Date.now() });
    DB_LOCAL.setHistory(_historyCache);
    return _historyCache;
  },
  addCase(c) {
    // Versión síncrona para compatibilidad (usa async internamente)
    this.addCaseAsync(c);
    if (!_historyCache) _historyCache = [];
    _historyCache.unshift({ ...c, id: c.id || Date.now() });
    DB_LOCAL.setHistory(_historyCache);
    return _historyCache;
  },
  async deleteCaseAsync(id) {
    if (mysqlOnline) {
      await apiFetch(`casos.php?id=${id}`, { method:'DELETE' });
    }
    _historyCache = (_historyCache || DB_LOCAL.getHistory()).filter(c => c.id !== id);
    DB_LOCAL.setHistory(_historyCache);
    return _historyCache;
  },
  deleteCase(id) {
    this.deleteCaseAsync(id);
    _historyCache = (_historyCache || DB_LOCAL.getHistory()).filter(c => c.id !== id);
    DB_LOCAL.setHistory(_historyCache);
    return _historyCache;
  },

  // Sesión (siempre local)
  getSession() { return DB_LOCAL.getSession(); },
  setSession(s) { DB_LOCAL.setSession(s); },
  clearSession() { DB_LOCAL.clearSession(); },

  // Tema
  getTheme() { return DB_LOCAL.getTheme(); },
  setTheme(t) { DB_LOCAL.set(DB_LOCAL.THEME, t); },

  // Notificaciones (local)
  getNotifs() { return DB_LOCAL.getNotifs(); },
  setNotifs(n) { DB_LOCAL.setNotifs(n); },
  addNotif(n)  { DB_LOCAL.addNotif(n); },
};

function defaultNotifications() {
  return [
    { title: 'Bienvenido a SensPulse', text: 'Sistema iniciado correctamente. Todos los datos se guardan localmente.', time: 'Hoy' },
    { title: 'Historial activo', text: 'Los casos se guardan en MySQL (phpMyAdmin) cuando XAMPP está activo, o en localStorage como respaldo automático.', time: 'Hoy' },
    { title: 'Modo pro activado', text: 'Acceso completo a dashboard, formulario, historial y exportación.', time: 'Hoy' },
  ];
}

// ══════════════════════════════════════
//  STATE
// ══════════════════════════════════════
let currentScreen = 0;
let currentUser = 'Invitado';
let currentRole = 'Demo';
let latestCase = null;
let riskChart = null;
let trendChart = null;
let backendLatency = 0;

// Seed inicial si el historial está vacío
const SEED_CASES = [
  { id: 10001, patient: 'Ana Torres', age: 29, sex: 'Femenino', symptoms: 'fiebre, tos', risks: 'contacto con enfermo', temperature: '38.9°C', title: 'Atención respiratoria', text: 'Monitoreo inmediato por fiebre y tos frecuente.', score: 82, priority: 'Alta', status: 'En observación', chartValues: [56,12,8,24], trendValues: [25,44,76,63], tags: ['Respiratorio','Guardado'], operator: 'Sistema demo', createdAt: new Date().toLocaleString('es-CO') },
  { id: 10002, patient: 'Luis Mejía', age: 44, sex: 'Masculino', symptoms: 'mareo, confusión', risks: 'estrés', temperature: '37.3°C', title: 'Observación neurológica', text: 'Seguimiento por mareo y confusión leve.', score: 61, priority: 'Media', status: 'En sala', chartValues: [12,14,54,20], trendValues: [18,31,49,41], tags: ['Neurológico'], operator: 'Sistema demo', createdAt: new Date().toLocaleString('es-CO') },
  { id: 10003, patient: 'María Rojas', age: 33, sex: 'Femenino', symptoms: 'malestar general', risks: 'sin hallazgos', temperature: '36.8°C', title: 'Orientación general', text: 'Sin patrón agudo claro. Revisión general recomendada.', score: 26, priority: 'Estable', status: 'Alta pronta', chartValues: [18,18,14,50], trendValues: [12,17,23,21], tags: ['General'], operator: 'Sistema demo', createdAt: new Date().toLocaleString('es-CO') },
];

async function seedIfEmpty() {
  const h = DB.getHistory();
  if (!h.length) {
    if (mysqlOnline) {
      for (const c of SEED_CASES) await apiFetch("casos.php", { method:"POST", body: JSON.stringify(c) });
      const r = await apiFetch("casos.php");
      if (r.ok) { _historyCache = r.data || []; DB_LOCAL.setHistory(_historyCache); }
    } else { DB.setHistory(SEED_CASES); }
  }
}

// ══════════════════════════════════════
//  DOM REFS
// ══════════════════════════════════════
const $  = id => document.getElementById(id);
const $$ = sel => document.querySelectorAll(sel);

// ══════════════════════════════════════
//  NOTIFICATIONS
// ══════════════════════════════════════
function renderNotifs() {
  const list = DB.getNotifs();
  $('notifList').innerHTML = list.map(n => `
    <div class="notif-item">
      <strong>${n.title}</strong>
      <p>${n.text}</p>
      <small>${n.time}</small>
    </div>
  `).join('') || '<div class="empty-state"><i class="fa-regular fa-bell-slash"></i><p>Sin notificaciones</p></div>';
}

function pushNotif(title, text) {
  DB.addNotif({ title, text });
  renderNotifs();
}

// ══════════════════════════════════════
//  NAVIGATION
// ══════════════════════════════════════
const SCREEN_TITLES = ['Inicio', 'Formulario', 'Dashboard', 'Historial'];
const SCREEN_CRUMBS = ['Panel principal', 'Registro de paciente', 'Análisis y métricas', 'Casos guardados'];

function goTo(index) {
  if (index === currentScreen) return;
  $$('.screen')[currentScreen].classList.remove('active');
  $$('.s-link').forEach((l, i) => l.classList.toggle('active', i === index));
  currentScreen = index;
  $$('.screen')[currentScreen].classList.add('active');
  $('pageTitle').textContent = SCREEN_TITLES[index];
  $('pageCrumb').textContent = SCREEN_CRUMBS[index];
  if (index === 2) setTimeout(buildCharts, 100);
  if (index === 3) renderHistory();
  if (index === 0) updateHomeStats();
}

// ══════════════════════════════════════
//  AUTHENTICATION
// ══════════════════════════════════════
const DEMO = { email: 'admin@senspulse.demo', password: 'senspulse2026' };

function setUser(name, role) {
  currentUser = name || 'Invitado';
  currentRole = role || 'Demo';
  $('sName').textContent = currentUser;
  $('sAvatar').textContent = currentUser.charAt(0).toUpperCase();
  $('userBadge').innerHTML = `<i class="fa-solid fa-user-doctor"></i> ${currentUser}`;
  $('dOperator') && ($('dOperator').textContent = role ? `${currentUser} · ${role}` : currentUser);
}

function showLoginError(msg) {
  const el = $('authMsg');
  el.className = 'auth-msg error';
  el.innerHTML = `<i class="fa-solid fa-triangle-exclamation"></i> ${msg}`;
}
function showLoginInfo(msg, type = '') {
  const el = $('authMsg');
  el.className = `auth-msg ${type}`;
  el.innerHTML = `<i class="fa-solid fa-circle-${type === 'success' ? 'check' : 'info'}"></i> ${msg}`;
}

// ══════════════════════════════════════
//  DIAGNOSIS ENGINE
// ══════════════════════════════════════
function diagnose() {
  const sint = $('fSintomas').value.toLowerCase();
  const risk = $('fRiesgos').value.toLowerCase();
  const temp = parseFloat($('fTemp').value) || 0;

  if ((sint.includes('tos') && sint.includes('fiebre')) || sint.includes('dificultad respiratoria')) {
    return { title: 'Atención respiratoria', text: 'Los datos sugieren una posible infección respiratoria. Se recomienda valoración médica para descartar tuberculosis u otra complicación pulmonar.', icon: 'fa-solid fa-lungs-virus', score: 78, priority: 'Alta', chartValues: [52,14,10,24], trendValues: [24,49,73,61], tags: ['Respiratorio','Prioridad alta','Seguimiento'], colorClass: 'rose' };
  } else if (sint.includes('escalofrios') || risk.includes('mosquito') || sint.includes('paludismo')) {
    return { title: 'Posible riesgo de malaria', text: 'Hay señales compatibles con malaria o infección vectorial. Se recomienda acudir a un centro de salud para pruebas diagnósticas.', icon: 'fa-solid fa-mosquito', score: 71, priority: 'Media', chartValues: [18,8,16,58], trendValues: [20,36,62,55], tags: ['Vector','Alerta','Control'], colorClass: 'amber' };
  } else if (sint.includes('dolor de pecho') || sint.includes('palpitaciones')) {
    return { title: 'Evaluación cardíaca sugerida', text: 'El caso muestra señales cardiovasculares. Se recomienda consulta médica pronta, especialmente si el dolor aumenta o se acompaña de mareo.', icon: 'fa-solid fa-heart-circle-check', score: 74, priority: 'Alta', chartValues: [16,57,11,16], trendValues: [18,33,66,58], tags: ['Cardíaco','Urgente','Monitoreo'], colorClass: 'rose' };
  } else if (sint.includes('mareo') || sint.includes('confusion') || sint.includes('desmayo') || sint.includes('confusión')) {
    return { title: 'Observación neurológica', text: 'Los síntomas pueden requerir valoración neurológica. Observar debilidad, alteración del habla o confusión persistente.', icon: 'fa-solid fa-brain', score: 64, priority: 'Media', chartValues: [14,12,58,16], trendValues: [16,28,53,47], tags: ['Neurológico','Observación','Control'], colorClass: 'violet' };
  } else if (temp >= 39) {
    return { title: 'Fiebre alta detectada', text: 'La temperatura registrada es elevada. Se recomienda atención médica oportuna para descartar infección o complicación aguda.', icon: 'fa-solid fa-temperature-high', score: 59, priority: 'Media', chartValues: [26,14,14,46], trendValues: [19,27,44,38], tags: ['Fiebre','Revisión','Seguimiento'], colorClass: 'amber' };
  } else {
    return { title: 'Orientación general', text: 'Los datos no muestran un patrón específico claro. Se recomienda valoración profesional para diagnóstico preciso.', icon: 'fa-solid fa-sparkles', score: 28, priority: 'Estable', chartValues: [20,16,14,50], trendValues: [10,18,26,24], tags: ['General','Visual','Interactivo'], colorClass: 'green' };
  }
}

function runAnalysis() {
  if (!$('fNombre').value.trim()) { alert('Por favor ingresa el nombre del paciente.'); return; }
  const result = diagnose();
  latestCase = {
    patient: $('fNombre').value.trim() || 'Sin nombre',
    age: $('fEdad').value || '--',
    sex: $('fSexo').value,
    symptoms: $('fSintomas').value || 'Sin datos',
    risks: $('fRiesgos').value || 'Sin datos',
    temperature: $('fTemp').value ? `${parseFloat($('fTemp').value).toFixed(1)}°C` : '--',
    title: result.title, text: result.text,
    score: result.score, priority: result.priority,
    status: result.priority,
    chartValues: result.chartValues, trendValues: result.trendValues,
    tags: result.tags, colorClass: result.colorClass,
    operator: currentRole ? `${currentUser} · ${currentRole}` : currentUser,
    createdAt: new Date().toLocaleString('es-CO'),
  };
  updateDashboard(latestCase, result);
  goTo(2);
}

function updateDashboard(c, result) {
  // KPIs
  $('kpiRisk').textContent = `${c.score}%`;
  $('kpiPriority').textContent = c.priority;
  $('kpiTemp').textContent = c.temperature;
  $('kpiTotal').textContent = DB.getHistory().length;
  $('kpi2Risk').textContent = `${c.score}%`;
  $('kpi2Priority').textContent = c.priority;

  // Patient summary
  $('dPatient').textContent = c.patient;
  $('dAge').textContent = c.age;
  $('dSex').textContent = c.sex;
  $('dOperator').textContent = c.operator;

  // Result box
  $('resultTitle').textContent = c.title;
  $('resultText').textContent = c.text;
  $('resultIcon').className = c.icon;

  // Tags
  const colorMap = { rose:'tag-rose', amber:'tag-amber', green:'tag-green', violet:'tag-violet', cyan:'tag-cyan' };
  $('resultTags').innerHTML = c.tags.map(t => `<span class="tag ${colorMap[c.colorClass] || 'tag-muted'}">${t}</span>`).join('');

  // Charts update
  if (riskChart) { riskChart.data.datasets[0].data = c.chartValues; riskChart.update(); }
  if (trendChart) { trendChart.data.datasets[0].data = c.trendValues; trendChart.data.datasets[1].data = c.trendValues.map(v => Math.max(5, v - 12)); trendChart.update(); }

  refreshTable();
}

// ══════════════════════════════════════
//  CHARTS
// ══════════════════════════════════════
function buildCharts() {
  const textColor = '#7a8a9e';
  const gridColor = 'rgba(255,255,255,.06)';

  if (riskChart) riskChart.destroy();
  if (trendChart) trendChart.destroy();

  const rv = latestCase?.chartValues || [34,22,18,26];
  const tv = latestCase?.trendValues || [22,38,56,44];

  riskChart = new Chart($('riskChart'), {
    type: 'doughnut',
    data: {
      labels: ['Respiratorio','Cardíaco','Neurológico','General'],
      datasets: [{ data: rv, backgroundColor: ['#00d4e8','#f43f5e','#8b5cf6','#10b981'], borderWidth: 0, hoverOffset: 6 }]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: { legend: { position: 'bottom', labels: { color: textColor, font: { family: 'DM Sans', size: 11 }, boxWidth: 10 } } },
      animation: { animateScale: true, duration: 900 }, cutout: '68%'
    }
  });

  trendChart = new Chart($('trendChart'), {
    type: 'line',
    data: {
      labels: ['Ingreso','Triage','Chequeo','Control'],
      datasets: [
        { label: 'Riesgo', data: tv, borderColor: '#00d4e8', backgroundColor: 'rgba(0,212,232,.08)', fill: true, tension: .4, pointRadius: 3, borderWidth: 2 },
        { label: 'Señales', data: tv.map(v => Math.max(5, v - 12)), borderColor: '#f43f5e', backgroundColor: 'rgba(244,63,94,.08)', fill: true, tension: .4, pointRadius: 3, borderWidth: 2 }
      ]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: { legend: { labels: { color: textColor, font: { family: 'DM Sans', size: 11 }, boxWidth: 10 } } },
      scales: {
        x: { ticks: { color: textColor, font: { size: 10 } }, grid: { color: gridColor } },
        y: { ticks: { color: textColor, font: { size: 10 } }, grid: { color: gridColor }, beginAtZero: true, max: 100 }
      },
      animation: { duration: 900, easing: 'easeOutQuart' }
    }
  });
}

// ══════════════════════════════════════
//  HISTORY
// ══════════════════════════════════════
function renderHistory() {
  const cases = DB.getHistory();
  const query = $('hSearch').value.toLowerCase().trim();
  const filter = $('hFilter').value;
  const filtered = cases.filter(c => {
    const mName = !query || c.patient.toLowerCase().includes(query);
    const mPri = filter === 'todos' || c.priority.toLowerCase().includes(filter);
    return mName && mPri;
  });

  $('sCases').textContent = cases.length;
  $('sHigh').textContent = cases.filter(c => c.priority.toLowerCase().includes('alta')).length;
  $('sLast').textContent = cases[0]?.patient || '--';
  updateHomeStats();

  if (!filtered.length) {
    $('historyList').innerHTML = `<div class="empty-state"><i class="fa-solid fa-folder-open"></i><p>No hay casos${query || filter !== 'todos' ? ' con ese filtro' : ''}. Analiza uno y guárdalo.</p></div>`;
    return;
  }

  const pillClass = p => { const l = p.toLowerCase(); return l.includes('alta') ? 'priority-alta' : l.includes('media') ? 'priority-media' : 'priority-estable'; };

  $('historyList').innerHTML = filtered.map(c => `
    <div class="history-card">
      <div class="hc-head">
        <div>
          <h3>${c.patient}</h3>
          <p>${c.title}</p>
        </div>
        <div style="display:flex;gap:7px;flex-wrap:wrap;">
          <span class="priority-pill ${pillClass(c.priority)}">${c.priority}</span>
          <span class="tag tag-muted">${c.score}%</span>
        </div>
      </div>
      <div class="hc-meta">
        <span><i class="fa-solid fa-user-doctor"></i> ${c.operator}</span>
        <span><i class="fa-solid fa-clock"></i> ${c.createdAt}</span>
        <span><i class="fa-solid fa-temperature-half"></i> ${c.temperature}</span>
      </div>
      <div class="hc-body">${c.text}</div>
      <div class="hc-tags">${(c.tags||[]).map(t => `<span class="tag tag-muted">${t}</span>`).join('')}</div>
      <div class="hc-actions">
        <button class="btn btn-ghost btn-xs" onclick="reopenCase(${c.id})"><i class="fa-solid fa-arrow-up-right-from-square"></i> Abrir</button>
        <button class="btn btn-danger btn-xs" onclick="deleteCase(${c.id})"><i class="fa-solid fa-trash"></i> Eliminar</button>
      </div>
    </div>
  `).join('');
}

function reopenCase(id) {
  const c = DB.getHistory().find(x => x.id === id);
  if (!c) return;
  $('fNombre').value = c.patient !== 'Sin nombre' ? c.patient : '';
  $('fEdad').value = c.age !== '--' ? c.age : '';
  $('fSexo').value = c.sex || 'Masculino';
  $('fTemp').value = c.temperature?.includes('°C') ? c.temperature.replace('°C','') : '';
  $('fSintomas').value = c.symptoms || '';
  $('fRiesgos').value = c.risks || '';
  latestCase = c;
  updateDashboard(c, c);
  goTo(2);
}

function deleteCase(id) {
  if (!confirm('¿Eliminar este caso del historial?')) return;
  DB.deleteCase(id);
  renderHistory();
  pushNotif('Caso eliminado', 'Se eliminó el registro del historial local.');
}

// ══════════════════════════════════════
//  TABLE
// ══════════════════════════════════════
function refreshTable() {
  const cases = DB.getHistory().slice(0, 6);
  const t0 = Date.now();
  setTimeout(() => {
    backendLatency = Date.now() - t0;
    $('kpiLatency').textContent = `${backendLatency} ms`;
    $('kpiTotal').textContent = DB.getHistory().length;

    if (!cases.length) {
      $('clinicalBody').innerHTML = `<tr><td colspan="4" class="empty-state"><i class="fa-solid fa-database"></i><p>Sin registros</p></td></tr>`;
      return;
    }
    const pillClass = p => { const l = p.toLowerCase(); return l.includes('alta') ? 'priority-alta' : l.includes('media') ? 'priority-media' : 'priority-estable'; };
    $('clinicalBody').innerHTML = cases.map(c => `
      <tr>
        <td><strong>${c.patient}</strong><small>${c.title}</small></td>
        <td><span class="priority-pill ${pillClass(c.priority)}">${c.priority}</span></td>
        <td>${c.score}%</td>
        <td>${c.status || c.priority}</td>
      </tr>
    `).join('');
  }, 300);
}

// ══════════════════════════════════════
//  HOME STATS
// ══════════════════════════════════════
function updateHomeStats() {
  const cases = DB.getHistory();
  const hEl = $('hsCases'); if (hEl) hEl.textContent = cases.length;
  const hEl2 = $('hsHigh'); if (hEl2) hEl2.textContent = cases.filter(c => c.priority.toLowerCase().includes('alta')).length;
}

// ══════════════════════════════════════
//  SAVE CASE
// ══════════════════════════════════════
function saveCase() {
  if (!latestCase) { alert('Primero analiza un caso para poder guardarlo.'); return; }
  DB.addCase(latestCase);
  pushNotif('Caso guardado', `Se guardó el registro de ${latestCase.patient}.`);
  $('kpiTotal').textContent = DB.getHistory().length;
  refreshTable();
  updateHomeStats();
}

// ══════════════════════════════════════
//  EXPORT PDF
// ══════════════════════════════════════
function exportPDF() {
  if (!latestCase) { alert('Primero analiza un caso para exportar el reporte.'); return; }
  // Ensure dashboard is visible
  $$('.screen').forEach((s, i) => s.classList.toggle('active', i === 2));
  currentScreen = 2;
  document.body.classList.add('printing');
  setTimeout(() => {
    window.print();
    setTimeout(() => document.body.classList.remove('printing'), 400);
  }, 200);
}

// ══════════════════════════════════════
//  LOADER
// ══════════════════════════════════════
function startLoader() {
  const bar = $('loaderBar');
  const steps = [20, 50, 75, 90, 100];
  steps.forEach((val, i) => {
    setTimeout(() => {
      bar.style.width = val + '%';
      if (val === 100) setTimeout(() => $('loaderOverlay').classList.add('hidden'), 300);
    }, 200 + i * 250);
  });
}

// ══════════════════════════════════════
//  BIND EVENTS
// ══════════════════════════════════════
document.addEventListener('DOMContentLoaded', () => {
  seedIfEmpty();

  // Sidebar nav
  $$('.s-link[data-screen]').forEach(btn => btn.addEventListener('click', () => goTo(+btn.dataset.screen)));
  // Tab go buttons
  $$('[data-go]').forEach(btn => btn.addEventListener('click', () => goTo(+btn.dataset.go)));

  // Chips
  $$('.chip[data-t]').forEach(chip => chip.addEventListener('click', () => {
    const ta = $(chip.dataset.t);
    const val = chip.dataset.v;
    if (!ta.value.toLowerCase().includes(val)) ta.value = ta.value ? ta.value + ', ' + val : val;
    chip.classList.add('active');
    setTimeout(() => chip.classList.remove('active'), 1000);
  }));

  // Analyze
  $('analyzeBtn').addEventListener('click', runAnalysis);
  $('clearFormBtn').addEventListener('click', () => {
    ['fNombre','fEdad','fTemp','fSintomas','fRiesgos'].forEach(id => $(id).value = '');
    $('fSexo').value = 'Masculino';
  });

  // Save / export
  $('saveCaseBtn').addEventListener('click', saveCase);
  $('exportBtn').addEventListener('click', exportPDF);
  $('refreshTableBtn').addEventListener('click', refreshTable);

  // History
  $('clearHistoryBtn').addEventListener('click', () => {
    if (!confirm('¿Eliminar todo el historial?')) return;
    DB.setHistory([]);
    pushNotif('Historial limpiado', 'Se eliminaron todos los casos guardados.');
    renderHistory();
    updateHomeStats();
  });
  $('hFilter').addEventListener('change', renderHistory);
  $('hSearch').addEventListener('input', renderHistory);

  // Notifications
  $('notifBtn').addEventListener('click', e => { e.stopPropagation(); $('notifPanel').classList.toggle('open'); });
  $('clearNotifsBtn').addEventListener('click', () => { DB.setNotifs([]); renderNotifs(); });
  document.addEventListener('click', e => { if (!$('notifPanel').contains(e.target) && e.target !== $('notifBtn')) $('notifPanel').classList.remove('open'); });

  // Logout
  $('logoutBtn').addEventListener('click', () => {
    DB.clearSession();
    setUser('Invitado','Demo');
    $('loginOverlay').classList.remove('hidden');
    showLoginInfo('Sesión cerrada. Vuelve a ingresar.');
  });

  // Fill demo
  $('fillDemoBtn').addEventListener('click', () => {
    $('fEmail').value = DEMO.email;
    $('fPass').value = DEMO.password;
    $('fName').value = $('fName').value || 'Doctor Luna';
    showLoginInfo('Credenciales demo cargadas. Puedes entrar.', 'success');
  });

  // Login (MySQL + fallback demo local)
  $('loginBtn').addEventListener('click', async () => {
    const email = $('fEmail').value.trim().toLowerCase();
    const pass  = $('fPass').value.trim();
    const name  = $('fName').value.trim() || 'Doctor Luna';
    const role  = $('fRole').value;
    $('loginBtn').disabled = true;
    showLoginInfo('Validando credenciales...');

    let loginOk = false, loginName = name, loginRole = role;

    if (mysqlOnline) {
      // Intentar login contra MySQL
      const r = await apiFetch('login.php', {
        method: 'POST',
        body: JSON.stringify({ email, password: pass, nombre: name, rol: role })
      });
      if (r.ok) {
        loginOk = true;
        loginName = r.nombre || name;
        loginRole = r.rol || role;
      } else {
        showLoginError(r.error || 'Credenciales inválidas.');
        $('loginBtn').disabled = false;
        return;
      }
    } else {
      // Fallback: demo local
      const validDemo   = email === DEMO.email && pass === DEMO.password;
      const validCustom = email && pass && name;
      loginOk = validDemo || validCustom;
      if (!loginOk) {
        showLoginError('Credenciales inválidas. Usa las claves demo o completa todos los campos.');
        $('loginBtn').disabled = false;
        return;
      }
    }

    if (loginOk) {
      setUser(loginName, loginRole);
      if ($('fRemember').checked) DB.setSession({ user: loginName, role: loginRole, email });
      showLoginInfo('Acceso correcto. Sesión iniciada.', 'success');
      setTimeout(() => $('loginOverlay').classList.add('hidden'), 500);
      pushNotif('Sesión iniciada', `${loginName} ingresó al panel.`);
      refreshTable();
    }
    $('loginBtn').disabled = false;
  });

  $('guestBtn').addEventListener('click', () => {
    setUser('Invitado','Demo');
    $('loginOverlay').classList.add('hidden');
    refreshTable();
    pushNotif('Invitado', 'Entraste sin autenticar. Algunas acciones son simuladas.');
  });

  // Theme toggle
  $('themeBtn').addEventListener('click', () => {
    // Simple dark-only (light mode would require full CSS rewrite)
    pushNotif('Tema', 'El modo oscuro está activo y optimizado para uso clínico nocturno.');
  });

  // Check saved session
  const sess = DB.getSession();
  if (sess?.user) {
    $('fName').value = sess.user;
    $('fRole').value = sess.role || 'Médico';
    $('fEmail').value = sess.email || DEMO.email;
    showLoginInfo(`Sesión recordada para ${sess.user}.`, 'success');
  }

  renderNotifs();
  buildCharts();
  startLoader();
  // Conectar a MySQL (async — actualiza historial cuando responde)
  checkMySQLConnection();
});

// Expose globals for inline handlers
window.reopenCase = reopenCase;
window.deleteCase = deleteCase;
</script>
</body>
</html>
