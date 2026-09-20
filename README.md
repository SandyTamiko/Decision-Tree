<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Decision Tree — Interactive Prototype</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#0A0C11;
    --surface:#12151C;
    --surface-2:#171B24;
    --surface-3:#1D2230;
    --border:rgba(255,255,255,0.08);
    --border-strong:rgba(255,255,255,0.16);
    --text:#E7E9EE;
    --text-dim:#9AA1AF;
    --text-faint:#5C6270;
    --data:#3B82F6;
    --logic:#A855F7;
    --actions:#22C55E;
    --advanced:#F59E0B;
    --run:#2F6FED;
    --run-dark:#1D4ED8;
    --danger:#F04747;
    --font-ui: Inter, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    --font-mono: "JetBrains Mono", ui-monospace, "SFMono-Regular", Menlo, monospace;
  }
  :root:not([data-theme="light"]){
    --bg:#0A0C11; --surface:#12151C; --text:#E7E9EE;
  }
  :root[data-theme="dark"]{
    --bg:#0A0C11; --surface:#12151C; --text:#E7E9EE;
  }
  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;height:100%;}
  body{
    background:var(--bg);
    color:var(--text);
    font-family:var(--font-ui);
    overflow:hidden;
    -webkit-font-smoothing:antialiased;
  }
  button{font-family:inherit;}
  input{font-family:inherit;}

  .app{
    display:flex;
    flex-direction:column;
    height:100vh;
    min-width:0;
  }

  /* ---------- Top bar ---------- */
  .topbar{
    display:flex;
    align-items:center;
    gap:14px;
    padding:0 16px;
    height:56px;
    background:var(--surface);
    border-bottom:1px solid var(--border);
    flex-shrink:0;
    position:relative;
    z-index:40;
  }
  .brand{
    display:flex;
    align-items:center;
    gap:8px;
    font-weight:700;
    font-size:15px;
    letter-spacing:-0.01em;
    white-space:nowrap;
  }
  .brand .mark{
    width:22px;height:22px;
    display:flex;align-items:center;justify-content:center;
    background:linear-gradient(135deg,var(--run),var(--logic));
    border-radius:6px;
    font-size:13px;
  }
  .tb-sep{width:1px;height:24px;background:var(--border);flex-shrink:0;}

  .filemenu{position:relative;}
  .tb-btn{
    display:flex;align-items:center;gap:6px;
    background:transparent;border:1px solid transparent;
    color:var(--text-dim);
    padding:7px 10px;border-radius:7px;
    font-size:13px;font-weight:500;
    cursor:pointer;
    transition:background .12s,color .12s;
    white-space:nowrap;
  }
  .tb-btn:hover{background:var(--surface-3);color:var(--text);}
  .tb-icon{
    display:flex;align-items:center;justify-content:center;
    width:30px;height:30px;border-radius:7px;
    color:var(--text-dim);background:transparent;border:none;cursor:pointer;
    transition:background .12s,color .12s;
    flex-shrink:0;
  }
  .tb-icon:hover{background:var(--surface-3);color:var(--text);}
  .tb-icon:disabled{opacity:.35;cursor:default;}
  .tb-icon:disabled:hover{background:transparent;}
  .tb-icon svg{width:16px;height:16px;}

  .dropdown{
    position:absolute;top:calc(100% + 6px);left:0;
    background:var(--surface-2);
    border:1px solid var(--border-strong);
    border-radius:10px;
    box-shadow:0 12px 32px rgba(0,0,0,.5);
    padding:6px;
    min-width:190px;
    z-index:60;
    display:none;
  }
  .dropdown.open{display:block;}
  .dropdown button{
    display:flex;align-items:center;gap:8px;
    width:100%;text-align:left;
    background:none;border:none;color:var(--text);
    padding:8px 10px;border-radius:6px;
    font-size:13px;cursor:pointer;
  }
  .dropdown button:hover{background:var(--surface-3);}
  .dropdown .dd-sep{height:1px;background:var(--border);margin:4px 2px;}

  .treename{
    background:transparent;
    border:1px solid transparent;
    color:var(--text-dim);
    font-size:13px;
    padding:7px 8px;
    border-radius:7px;
    width:140px;
    min-width:0;
  }
  .treename:hover{background:var(--surface-3);}
  .treename:focus{outline:none;background:var(--surface-3);color:var(--text);border-color:var(--border-strong);}

  .demoselect{position:relative;flex-shrink:0;}
  .demoselect .tb-btn{background:var(--surface-3);color:var(--text);}
  .demo-badge{
    font-family:var(--font-mono);
    font-size:10.5px;
    color:var(--text-faint);
  }

  .search-wrap{
    flex:1;
    display:flex;align-items:center;gap:8px;
    background:var(--surface-2);
    border:1px solid var(--border);
    border-radius:8px;
    padding:0 10px;
    height:34px;
    max-width:340px;
    min-width:120px;
  }
  .search-wrap svg{width:14px;height:14px;color:var(--text-faint);flex-shrink:0;}
  .search-wrap input{
    background:none;border:none;color:var(--text);font-size:13px;width:100%;
  }
  .search-wrap input:focus{outline:none;}
  .search-wrap input::placeholder{color:var(--text-faint);}

  .tb-right{display:flex;align-items:center;gap:8px;margin-left:auto;flex-shrink:0;}

  .stat-pill{
    font-family:var(--font-mono);
    font-size:11px;
    color:var(--text-dim);
    background:var(--surface-2);
    border:1px solid var(--border);
    padding:5px 9px;
    border-radius:7px;
    white-space:nowrap;
  }
  .stat-pill b{color:var(--text);font-weight:600;}

  .run-btn{
    display:flex;align-items:center;gap:7px;
    background:linear-gradient(135deg,var(--run),var(--run-dark));
    color:#fff;border:none;
    padding:8px 16px;border-radius:8px;
    font-size:13px;font-weight:600;
    cursor:pointer;
    box-shadow:0 4px 14px rgba(47,111,237,.35);
    transition:transform .08s, box-shadow .12s;
  }
  .run-btn:hover{box-shadow:0 6px 18px rgba(47,111,237,.5);}
  .run-btn:active{transform:translateY(1px);}
  .run-btn svg{width:13px;height:13px;}
  .run-btn.running{opacity:.7;cursor:progress;}

  /* ---------- History / Settings panels ---------- */
  .popover{
    position:absolute;top:calc(100% + 6px);right:0;
    background:var(--surface-2);
    border:1px solid var(--border-strong);
    border-radius:10px;
    box-shadow:0 12px 32px rgba(0,0,0,.5);
    width:280px;
    z-index:60;
    display:none;
    overflow:hidden;
  }
  .popover.open{display:block;}
  .popover-head{
    padding:10px 12px;
    font-size:12px;font-weight:600;color:var(--text);
    border-bottom:1px solid var(--border);
  }
  .popover-body{max-height:280px;overflow-y:auto;padding:6px;}
  .hist-row{
    display:flex;flex-direction:column;gap:1px;
    padding:8px 8px;border-radius:6px;font-size:12px;
  }
  .hist-row:hover{background:var(--surface-3);}
  .hist-row .t{color:var(--text);}
  .hist-row .s{color:var(--text-faint);font-size:10.5px;font-family:var(--font-mono);}
  .hist-empty{padding:18px 12px;color:var(--text-faint);font-size:12px;text-align:center;}
  .settings-row{
    display:flex;align-items:center;justify-content:space-between;
    padding:10px 12px;font-size:12.5px;color:var(--text);
  }
  .switch{
    position:relative;width:34px;height:19px;flex-shrink:0;
  }
  .switch input{opacity:0;width:0;height:0;position:absolute;}
  .switch .track{
    position:absolute;inset:0;background:var(--surface-3);border:1px solid var(--border-strong);
    border-radius:20px;cursor:pointer;transition:background .15s;
  }
  .switch .knob{
    position:absolute;top:2px;left:2px;width:13px;height:13px;border-radius:50%;
    background:var(--text-dim);transition:left .15s, background .15s;
  }
  .switch input:checked + .track{background:var(--run);border-color:var(--run);}
  .switch input:checked + .track .knob{left:17px;background:#fff;}

  /* ---------- Body layout ---------- */
  .body{flex:1;display:flex;min-height:0;position:relative;}

  /* ---------- Sidebar ---------- */
  .sidebar{
    width:238px;flex-shrink:0;
    background:var(--surface);
    border-right:1px solid var(--border);
    display:flex;flex-direction:column;
    transition:width .16s ease, margin-left .16s ease;
  }
  .sidebar.collapsed{width:0;margin-left:0;border-right:none;overflow:hidden;}
  .sidebar-head{
    display:flex;align-items:center;justify-content:space-between;
    padding:14px 14px 10px;
  }
  .sidebar-head h2{font-size:14px;margin:0;font-weight:600;}
  .collapse-btn{
    width:26px;height:26px;border-radius:6px;border:1px solid var(--border);
    background:transparent;color:var(--text-dim);cursor:pointer;
    display:flex;align-items:center;justify-content:center;
  }
  .collapse-btn:hover{background:var(--surface-3);}
  .collapse-btn svg{width:13px;height:13px;}

  .palette{flex:1;overflow-y:auto;padding:2px 10px 14px;}
  .pal-group{margin-bottom:4px;}
  .pal-group-head{
    display:flex;align-items:center;gap:8px;
    padding:9px 6px;cursor:pointer;border-radius:7px;
    user-select:none;
  }
  .pal-group-head:hover{background:var(--surface-2);}
  .pal-group-head .name{flex:1;font-size:12.5px;font-weight:600;color:var(--text-dim);}
  .pal-group-head .count{
    font-family:var(--font-mono);font-size:10.5px;
    background:var(--surface-3);color:var(--text-dim);
    padding:1px 7px;border-radius:5px;
  }
  .pal-group-head .chev{
    width:13px;height:13px;color:var(--text-faint);
    transition:transform .14s;
  }
  .pal-group.closed .chev{transform:rotate(-90deg);}
  .pal-group.closed .pal-items{display:none;}
  .pal-items{padding:2px 0 8px 2px;}
  .pal-item{
    display:flex;align-items:center;gap:9px;
    padding:8px 8px;border-radius:7px;
    cursor:grab;
    font-size:13px;color:var(--text);
    border:1px solid transparent;
  }
  .pal-item:hover{background:var(--surface-2);border-color:var(--border);}
  .pal-item.dimmed{opacity:.28;}
  .pal-ic{
    width:24px;height:24px;border-radius:6px;flex-shrink:0;
    display:flex;align-items:center;justify-content:center;
  }
  .pal-ic svg{width:13px;height:13px;}
  .pal-ic.data{background:rgba(59,130,246,.16);color:var(--data);}
  .pal-ic.logic{background:rgba(168,85,247,.16);color:var(--logic);}
  .pal-ic.actions{background:rgba(34,197,94,.16);color:var(--actions);}
  .pal-ic.advanced{background:rgba(245,158,11,.16);color:var(--advanced);}

  .sidebar-foot{
    padding:11px 14px;border-top:1px solid var(--border);
    font-size:11px;color:var(--text-faint);line-height:1.5;
  }

  /* ---------- Canvas ---------- */
  .canvas-outer{
    flex:1;position:relative;overflow:hidden;
    background-color:var(--bg);
    background-image:radial-gradient(circle, rgba(255,255,255,0.055) 1px, transparent 1px);
    background-size:22px 22px;
  }
  .canvas-inner{
    position:absolute;top:0;left:0;
    width:3000px;height:1400px;
    transform-origin:0 0;
  }
  svg.edges{position:absolute;top:0;left:0;width:100%;height:100%;pointer-events:none;overflow:visible;}
  .edge-path{fill:none;stroke-width:2;stroke-dasharray:5 5;pointer-events:none;}
  .edge-hit{fill:none;stroke:transparent;stroke-width:14;pointer-events:stroke;cursor:pointer;}
  .edge-hit:hover + .edge-path{stroke:var(--danger)!important;stroke-dasharray:3 3;}
  .edge-port{fill:var(--bg);stroke-width:2;}

  .empty-hint{
    position:absolute;top:40%;left:50%;transform:translate(-50%,-50%);
    text-align:center;color:var(--text-faint);
    max-width:280px;
  }
  .empty-hint svg{width:34px;height:34px;margin-bottom:10px;opacity:.5;}
  .empty-hint h3{font-size:14px;margin:0 0 6px;color:var(--text-dim);font-weight:600;}
  .empty-hint p{font-size:12.5px;margin:0;line-height:1.5;}

  .node{
    position:absolute;
    width:216px;
    background:var(--surface-2);
    border:1px solid var(--border-strong);
    border-radius:11px;
    box-shadow:0 6px 18px rgba(0,0,0,.35);
    cursor:grab;
    user-select:none;
    z-index:5;
  }
  .node:active{cursor:grabbing;}
  .node.selected{
    border-color:#fff;
    box-shadow:0 0 0 2px rgba(255,255,255,.5), 0 8px 22px rgba(0,0,0,.45);
    z-index:10;
  }
  .node.running{box-shadow:0 0 0 2px #fff, 0 0 22px rgba(47,111,237,.6);}
  .node.done-success{box-shadow:0 0 0 2px var(--actions);}
  .node.done-skipped{opacity:.5;}
  .node-head{
    display:flex;align-items:center;gap:8px;
    padding:9px 11px;
    border-radius:10px 10px 0 0;
    font-weight:600;font-size:13.5px;color:#fff;
  }
  .node-head svg{width:14px;height:14px;flex-shrink:0;}
  .node-head.data{background:linear-gradient(135deg,#3B82F6,#2563EB);}
  .node-head.logic{background:linear-gradient(135deg,#A855F7,#9333EA);}
  .node-head.actions{background:linear-gradient(135deg,#22C55E,#16A34A);}
  .node-head.advanced{background:linear-gradient(135deg,#F59E0B,#D97706);}
  .node-body{padding:10px 11px 12px;}
  .node-body .sub{font-size:11.5px;color:var(--text-dim);line-height:1.4;margin:0 0 9px;}
  .node-tag{
    display:inline-block;font-family:var(--font-mono);font-size:10px;
    color:var(--text-dim);background:var(--surface-3);
    border:1px solid var(--border);
    padding:2px 7px;border-radius:5px;
  }
  .node-port{
    position:absolute;width:16px;height:16px;border-radius:50%;
    top:50%;transform:translateY(-50%);
    display:flex;align-items:center;justify-content:center;
    cursor:crosshair;z-index:8;
  }
  .node-port::before{
    content:'';width:11px;height:11px;border-radius:50%;
    background:var(--text-faint);border:2px solid var(--bg);
    transition:background .12s, transform .12s;
  }
  .node-port.in{left:-8px;}
  .node-port.out{right:-8px;}
  .node-port:hover::before{background:var(--run);transform:scale(1.25);}
  .node-port.port-target::before{background:var(--actions);transform:scale(1.5);}
  .temp-edge{stroke:#2F6FED !important;stroke-width:2.5;opacity:.9;}
  .node-del{
    position:absolute;top:-9px;right:-9px;
    width:20px;height:20px;border-radius:50%;
    background:var(--danger);color:#fff;border:2px solid var(--bg);
    display:none;align-items:center;justify-content:center;cursor:pointer;
    z-index:11;
  }
  .node.selected .node-del{display:flex;}
  .node-del svg{width:10px;height:10px;}

  /* ---------- Zoom controls ---------- */
  .zoom-controls{
    position:absolute;left:16px;bottom:16px;
    display:flex;flex-direction:column;gap:5px;
    background:var(--surface-2);border:1px solid var(--border-strong);
    border-radius:9px;padding:4px;
    z-index:30;
  }
  .zoom-controls button{
    width:30px;height:30px;border:none;background:transparent;color:var(--text-dim);
    border-radius:6px;cursor:pointer;display:flex;align-items:center;justify-content:center;
  }
  .zoom-controls button:hover{background:var(--surface-3);color:var(--text);}
  .zoom-controls button svg{width:14px;height:14px;}
  .zoom-controls .zpct{
    font-family:var(--font-mono);font-size:9.5px;color:var(--text-faint);
    text-align:center;padding:2px 0;
  }
  .zoom-controls hr{border:none;border-top:1px solid var(--border);margin:2px 0;width:100%;}

  /* ---------- Minimap ---------- */
  .minimap{
    position:absolute;right:16px;bottom:16px;
    width:200px;height:120px;
    background:var(--surface-2);
    border:1px solid var(--border-strong);
    border-radius:9px;
    overflow:hidden;
    z-index:30;
  }
  .minimap .mm-node{position:absolute;border-radius:2px;}

  /* ---------- Bottom panel ---------- */
  .bottompanel{
    flex-shrink:0;
    background:var(--surface);
    border-top:1px solid var(--border);
    display:flex;flex-direction:column;
    height:230px;
    transition:height .16s ease;
  }
  .bottompanel.collapsed{height:40px;}
  .bp-tabs{
    display:flex;align-items:center;gap:2px;
    padding:0 12px;height:40px;flex-shrink:0;
    border-bottom:1px solid var(--border);
  }
  .bp-tab{
    background:none;border:none;color:var(--text-faint);
    font-size:12.5px;font-weight:600;padding:9px 12px;
    cursor:pointer;border-bottom:2px solid transparent;
    display:flex;align-items:center;gap:6px;
  }
  .bp-tab.active{color:var(--text);border-bottom-color:var(--run);}
  .bp-tab .dot{width:6px;height:6px;border-radius:50%;background:var(--actions);}
  .bp-toggle{
    margin-left:auto;width:26px;height:26px;border:none;background:transparent;
    color:var(--text-dim);border-radius:6px;cursor:pointer;
    display:flex;align-items:center;justify-content:center;
  }
  .bp-toggle:hover{background:var(--surface-3);}
  .bp-toggle svg{width:13px;height:13px;transition:transform .16s;}
  .bottompanel.collapsed .bp-toggle svg{transform:rotate(180deg);}
  .bp-content{flex:1;overflow-y:auto;padding:14px 18px;}
  .bottompanel.collapsed .bp-content{display:none;}

  .bp-empty{
    color:var(--text-faint);font-size:12.5px;
    display:flex;align-items:center;gap:8px;height:100%;
  }
  .bp-empty svg{width:16px;height:16px;}

  .prop-grid{display:flex;flex-direction:column;gap:12px;max-width:520px;}
  .prop-row label{display:block;font-size:11px;color:var(--text-faint);margin-bottom:5px;font-weight:600;letter-spacing:.02em;}
  .prop-row input, .prop-row textarea{
    width:100%;background:var(--surface-2);border:1px solid var(--border);
    color:var(--text);border-radius:7px;padding:8px 10px;font-size:13px;
  }
  .prop-row input:focus, .prop-row textarea:focus{outline:none;border-color:var(--run);}
  .prop-meta{display:flex;gap:18px;font-size:11.5px;color:var(--text-dim);margin-top:2px;}
  .prop-meta b{color:var(--text);}

  .results-list{display:flex;flex-direction:column;gap:6px;}
  .res-row{
    display:flex;align-items:center;gap:10px;
    background:var(--surface-2);border:1px solid var(--border);
    border-radius:8px;padding:9px 12px;
    font-size:12.5px;
  }
  .res-status{width:15px;height:15px;flex-shrink:0;border-radius:50%;display:flex;align-items:center;justify-content:center;}
  .res-status.success{background:rgba(34,197,94,.18);color:var(--actions);}
  .res-status.skipped{background:rgba(255,255,255,.08);color:var(--text-faint);}
  .res-status svg{width:9px;height:9px;}
  .res-name{font-weight:600;min-width:150px;color:var(--text);}
  .res-out{font-family:var(--font-mono);font-size:11.5px;color:var(--text-dim);flex:1;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;}
  .res-pill{font-size:10px;font-family:var(--font-mono);padding:2px 7px;border-radius:5px;flex-shrink:0;}
  .res-pill.success{background:rgba(34,197,94,.14);color:var(--actions);}
  .res-pill.skipped{background:rgba(255,255,255,.06);color:var(--text-faint);}
  .run-complete-banner{
    margin-top:10px;display:flex;align-items:center;gap:8px;
    background:rgba(34,197,94,.12);border:1px solid rgba(34,197,94,.3);
    color:var(--actions);padding:9px 12px;border-radius:8px;
    font-size:12.5px;font-weight:600;
  }
  .run-complete-banner svg{width:14px;height:14px;}

  ::-webkit-scrollbar{width:9px;height:9px;}
  ::-webkit-scrollbar-thumb{background:var(--surface-3);border-radius:6px;border:2px solid var(--surface);}
  ::-webkit-scrollbar-track{background:transparent;}

  @media (max-width:760px){
    .sidebar{position:absolute;left:0;top:0;bottom:0;z-index:50;height:100%;}
    .treename{display:none;}
    .search-wrap{max-width:none;}
  }
</style>
</head>
<body>
<div class="app" id="app">

  <div class="topbar">
    <div class="brand"><span class="mark">🌳</span>Decision Tree</div>

    <div class="filemenu">
      <button class="tb-btn" id="fileBtn">File</button>
      <div class="dropdown" id="fileMenu">
        <button data-act="new"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><path d="M14 2v6h6"/></svg>New flow</button>
        <button data-act="reset"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M3 12a9 9 0 1 0 3-6.7"/><path d="M3 4v5h5"/></svg>Reset this demo</button>
        <div class="dd-sep"></div>
        <button data-act="export"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M12 3v12"/><path d="M7 10l5 5 5-5"/><path d="M5 21h14"/></svg>Export as JSON</button>
      </div>
    </div>

    <button class="tb-icon" id="undoBtn" title="Undo">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M3 7v6h6"/><path d="M3 13a9 9 0 1 1 3 6.7"/></svg>
    </button>
    <button class="tb-icon" id="redoBtn" title="Redo">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 7v6h-6"/><path d="M21 13a9 9 0 1 0-3 6.7"/></svg>
    </button>

    <div class="tb-sep"></div>

    <input class="treename" id="treeName" value="My Decision Tree" spellcheck="false">

    <div class="demoselect">
      <button class="tb-btn" id="demoBtn">
        <span id="demoLabel">Demo 1</span>
        <span class="demo-badge">▾</span>
      </button>
      <div class="dropdown" id="demoMenu"></div>
    </div>

    <div class="search-wrap">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="11" cy="11" r="7"/><path d="M21 21l-4.3-4.3"/></svg>
      <input id="searchInput" placeholder="Search nodes...">
    </div>

    <div class="tb-right">
      <div class="stat-pill">Nodes: <b id="nodeCount">0</b></div>
      <div class="stat-pill">Connections: <b id="edgeCount">0</b></div>

      <div class="filemenu">
        <button class="tb-icon" id="histBtn" title="History">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M3 12a9 9 0 1 0 3-6.7"/><path d="M3 4v5h5"/><path d="M12 7v5l4 2"/></svg>
        </button>
        <div class="popover" id="histPanel" style="width:260px;">
          <div class="popover-head">Activity history</div>
          <div class="popover-body" id="histList"></div>
        </div>
      </div>

      <div class="filemenu">
        <button class="tb-icon" id="setBtn" title="Settings">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 1 1-2.83 2.83l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-4 0v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 1 1-2.83-2.83l.06-.06A1.65 1.65 0 0 0 4.6 15a1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1 0-4h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 1 1 2.83-2.83l.06.06A1.65 1.65 0 0 0 9 4.6a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 4 0v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 1 1 2.83 2.83l-.06.06A1.65 1.65 0 0 0 19.4 9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 0 4h-.09a1.65 1.65 0 0 0-1.51 1z"/></svg>
        </button>
        <div class="popover" id="setPanel">
          <div class="popover-head">Canvas settings</div>
          <div class="settings-row">Snap to grid
            <label class="switch"><input type="checkbox" id="snapToggle"><span class="track"><span class="knob"></span></span></label>
          </div>
          <div class="settings-row">Show minimap
            <label class="switch"><input type="checkbox" id="mmToggle" checked><span class="track"><span class="knob"></span></span></label>
          </div>
        </div>
      </div>

      <button class="run-btn" id="runBtn">
        <svg viewBox="0 0 24 24" fill="currentColor"><path d="M8 5v14l11-7z"/></svg>
        Run
      </button>
    </div>
  </div>

  <div class="body">
    <div class="sidebar" id="sidebar">
      <div class="sidebar-head">
        <h2>Node Palette</h2>
        <button class="collapse-btn" id="collapseBtn" title="Collapse">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="4" width="18" height="16" rx="2"/><path d="M9 4v16"/></svg>
        </button>
      </div>
      <div class="palette" id="palette"></div>
      <div class="sidebar-foot">Click a node type to drop it on the canvas. Drag from a node's right-side dot to another node's left-side dot to connect them — click a connection line to remove it.</div>
    </div>

    <div class="canvas-outer" id="canvasOuter">
      <div class="canvas-inner" id="canvasInner">
        <svg class="edges" id="edgesLayer"></svg>
        <div id="nodesLayer"></div>
      </div>
      <div class="empty-hint" id="emptyHint" style="display:none;">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><path d="M14 2v6h6"/></svg>
        <h3>Empty canvas</h3>
        <p>Add a node from the palette on the left, or pick a demo flow above to see a filled-out example.</p>
      </div>

      <div class="zoom-controls">
        <button id="zoomIn" title="Zoom in"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="11" cy="11" r="7"/><path d="M21 21l-4.3-4.3"/><path d="M11 8v6M8 11h6"/></svg></button>
        <div class="zpct" id="zoomPct">100%</div>
        <button id="zoomOut" title="Zoom out"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="11" cy="11" r="7"/><path d="M21 21l-4.3-4.3"/><path d="M8 11h6"/></svg></button>
        <hr>
        <button id="zoomFit" title="Fit view"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M8 3H5a2 2 0 0 0-2 2v3"/><path d="M21 8V5a2 2 0 0 0-2-2h-3"/><path d="M3 16v3a2 2 0 0 0 2 2h3"/><path d="M16 21h3a2 2 0 0 0 2-2v-3"/></svg></button>
      </div>

      <div class="minimap" id="minimap"></div>
    </div>
  </div>

  <div class="bottompanel" id="bottomPanel">
    <div class="bp-tabs">
      <button class="bp-tab active" data-tab="properties" id="tabProperties">Properties</button>
      <button class="bp-tab" data-tab="run" id="tabRun"><span class="dot" id="runDot" style="display:none;"></span>Run results</button>
      <button class="bp-toggle" id="bpToggle" title="Collapse panel">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M18 15l-6-6-6 6"/></svg>
      </button>
    </div>
    <div class="bp-content" id="bpContent"></div>
  </div>

</div>

<script>
(function(){

  /* ---------------- Icons ---------------- */
  const ICONS = {
    db: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><ellipse cx="12" cy="5" rx="8" ry="3"/><path d="M4 5v14c0 1.7 3.6 3 8 3s8-1.3 8-3V5"/><path d="M4 12c0 1.7 3.6 3 8 3s8-1.3 8-3"/></svg>',
    split: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="6" cy="6" r="2.5"/><circle cx="6" cy="18" r="2.5"/><circle cx="18" cy="6" r="2.5"/><path d="M6 8.5V15a3 3 0 0 0 3 3h.5"/><path d="M6 8.5v0"/><path d="M15.5 6H12"/></svg>',
    calc: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="4" y="2" width="16" height="20" rx="2"/><path d="M8 6h8"/><path d="M8 11h.01M12 11h.01M16 11h.01M8 15h.01M12 15h.01M16 15h.01M8 19h.01M12 19h.01"/></svg>',
    filter: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M22 3H2l8 9.5V19l4 2v-8.5z"/></svg>',
    bell: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M18 8a6 6 0 0 0-12 0c0 7-3 9-3 9h18s-3-2-3-9"/><path d="M13.7 21a2 2 0 0 1-3.4 0"/></svg>',
    file: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><path d="M14 2v6h6"/><path d="M9 13h6M9 17h6"/></svg>',
    share: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="6" cy="12" r="2.6"/><circle cx="18" cy="6" r="2.6"/><circle cx="18" cy="18" r="2.6"/><path d="M8.3 10.7l7.4-4.4M8.3 13.3l7.4 4.4"/></svg>',
    code: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M9 18l-6-6 6-6"/><path d="M15 6l6 6-6 6"/></svg>',
    trash: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M3 6h18"/><path d="M8 6V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/><path d="M19 6l-1 14a2 2 0 0 1-2 2H8a2 2 0 0 1-2-2L5 6"/></svg>',
    check: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3"><path d="M20 6L9 17l-5-5"/></svg>',
    skip: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><path d="M9 9l6 6M15 9l-6 6"/></svg>',
    hash: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M4 9h16M4 15h16M10 3L8 21M16 3l-2 18"/></svg>',
    invert: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="9"/><path d="M5.6 5.6l12.8 12.8"/></svg>',
    mail: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="5" width="18" height="14" rx="2"/><path d="M3 7l9 6 9-6"/></svg>',
    tag: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M20.6 12.3L12.3 20.6a1.9 1.9 0 0 1-2.7 0l-7.2-7.2a1.9 1.9 0 0 1 0-2.7L10.7 2.4A1.9 1.9 0 0 1 12 2h6.6A2.4 2.4 0 0 1 21 4.4V11a1.9 1.9 0 0 1-.4 1.3z"/><circle cx="15.5" cy="7.5" r="1.2"/></svg>',
    search: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="11" cy="11" r="7"/><path d="M21 21l-4.3-4.3"/></svg>'
  };

  /* ---------------- Palette definition ---------------- */
  const PALETTE = [
    { key:'data', name:'Data', items:[
      {type:'Data Input', icon:'db', sub:'Pull data into the flow'},
      {type:'SQL Query', icon:'db', sub:'Run a query against a table'},
      {type:'Column Selector', icon:'db', sub:'Select specific columns from a result'},
      {type:'Static Number', icon:'hash', sub:'Inject a fixed numeric value'},
      {type:'Data Output', icon:'db', sub:'Write results somewhere'}
    ]},
    { key:'logic', name:'Logic', items:[
      {type:'Conditional', icon:'split', sub:'Branch based on a condition'},
      {type:'Two-Way Split', icon:'split', sub:'Split a flow into two branches'},
      {type:'Not', icon:'invert', sub:'Invert a boolean value'},
      {type:'Calculation', icon:'calc', sub:'Perform a calculation'},
      {type:'Comparison', icon:'filter', sub:'Compare a value to a threshold'}
    ]},
    { key:'actions', name:'Actions', items:[
      {type:'Alarm', icon:'bell', sub:'Trigger an alarm notification'},
      {type:'Send Email', icon:'mail', sub:'Notify a person or team by email'},
      {type:'Report', icon:'file', sub:'Generate a report'},
      {type:'Create Annotation', icon:'tag', sub:'Log a reviewable annotation'},
      {type:'Workflow', icon:'share', sub:'Kick off a downstream workflow'}
    ]},
    { key:'advanced', name:'Advanced', items:[
      {type:'Annotation Query', icon:'search', sub:'Pull existing annotations from a table'},
      {type:'Annotation Selector', icon:'tag', sub:'Select fields from an annotation'},
      {type:'Annotation Update', icon:'tag', sub:'Update fields on an annotation'},
      {type:'Attribute Selector', icon:'filter', sub:'Select an attribute from a dataset'},
      {type:'Script', icon:'code', sub:'Run a custom script step'}
    ]}
  ];
  const TYPE_CATEGORY = {};
  const TYPE_ICON = {};
  PALETTE.forEach(g=>g.items.forEach(it=>{TYPE_CATEGORY[it.type]=g.key; TYPE_ICON[it.type]=it.icon;}));

  /* ---------------- Demo flows (using the original tool's node vocabulary) ---------------- */
  const DEMOS = {
    demo1:{
      label:'Demo 1', title:'Leak Detection Alert',
      desc:'Pulls sensor readings, checks the pressure delta against a threshold, and routes the result to an annotation + attribute audit trail before a final pass/fail check.',
      nodes:[
        {id:'n1', ptype:'SQL Query', label:'PortalSQLQuery', sub:'Pull last 24h pressure log', x:40, y:140},
        {id:'n2', ptype:'Column Selector', label:'SQLSelector', sub:'Select pressure + timestamp columns', x:300, y:140},
        {id:'n3', ptype:'Calculation', label:'Sum_Max_Min', sub:'Find min / max pressure delta', x:560, y:60},
        {id:'n4', ptype:'Static Number', label:'StaticNumber', sub:'Safe threshold: 6.0 psi', x:560, y:300},
        {id:'n5', ptype:'Comparison', label:'Comparison', sub:'delta > threshold ?', x:820, y:170},
        {id:'n6', ptype:'Two-Way Split', label:'TwoWaySplit', sub:'true → audit path · false → skip', x:1080, y:60},
        {id:'n7', ptype:'Not', label:'Not', sub:'Invert for the skip path', x:1080, y:300},
        {id:'n8', ptype:'Annotation Query', label:'AnnotationQuery', sub:'Pull prior annotations for this sensor', x:1340, y:20},
        {id:'n9', ptype:'Create Annotation', label:'CreateAnnotation', sub:'Log a "no action" note', x:1340, y:340},
        {id:'n10', ptype:'Annotation Selector', label:'AnnotationSelector', sub:'Select the latest annotation', x:1600, y:140},
        {id:'n11', ptype:'Annotation Update', label:'AnnotationUpdate', sub:'Attach this reading to the annotation', x:1860, y:140},
        {id:'n12', ptype:'Attribute Selector', label:'AttributeSelector', sub:'Pull the sensor\'s calibration offset', x:2120, y:20},
        {id:'n13', ptype:'Attribute Selector', label:'AttributeSelector', sub:'Pull the sensor\'s last service date', x:2120, y:260},
        {id:'n14', ptype:'Calculation', label:'BasicMath', sub:'Adjust delta by calibration offset', x:2380, y:20},
        {id:'n15', ptype:'Static Number', label:'StaticNumber', sub:'Final pass/fail limit: 8.0 psi', x:2380, y:260},
        {id:'n16', ptype:'Comparison', label:'Comparison', sub:'adjusted delta > limit → SendEmail', x:2640, y:140}
      ],
      edges:[
        {from:'n1',to:'n2'},{from:'n2',to:'n3'},
        {from:'n3',to:'n5'},{from:'n4',to:'n5'},
        {from:'n5',to:'n6'},
        {from:'n6',to:'n8',port:'true'},{from:'n6',to:'n7',port:'false'},
        {from:'n7',to:'n9'},
        {from:'n8',to:'n10'},{from:'n10',to:'n11'},
        {from:'n11',to:'n12'},{from:'n11',to:'n13'},
        {from:'n12',to:'n14'},{from:'n13',to:'n14'},
        {from:'n14',to:'n16'},{from:'n15',to:'n16'}
      ],
      results:{
        n1:{status:'success', out:'[212 rows] pressure_log'},
        n2:{status:'success', out:'2 columns selected'},
        n3:{status:'success', out:'{min:41.2, max:48.9, delta:7.7}'},
        n4:{status:'success', out:'value: 6.0'},
        n5:{status:'success', out:'true — 7.7 > 6.0'},
        n6:{status:'success', out:'branch → true (audit path)'},
        n7:{status:'skipped', out:'not evaluated'},
        n8:{status:'success', out:'[3] prior annotations'},
        n9:{status:'skipped', out:'not evaluated'},
        n10:{status:'success', out:'annotation_id: AN-4471'},
        n11:{status:'success', out:'reading attached to AN-4471'},
        n12:{status:'success', out:'calibration_offset: 0.4'},
        n13:{status:'success', out:'last_service: 2026-06-02'},
        n14:{status:'success', out:'adjusted delta: 8.1'},
        n15:{status:'success', out:'value: 8.0'},
        n16:{status:'success', out:'true — 8.1 > 8.0 → SendEmail'}
      }
    },
    demo2:{
      label:'Demo 2', title:'Weekly Compliance Report',
      desc:'Pulls the week\'s readings, summarizes them, and generates a scheduled report for plant managers.',
      nodes:[
        {id:'n1', ptype:'SQL Query', label:'PartialSQLQuery', sub:'Filter readings to last 7 days', x:60, y:170, tag:'sql'},
        {id:'n2', ptype:'Column Selector', label:'SQLSelector', sub:'Select relevant columns', x:330, y:170, tag:'sql'},
        {id:'n3', ptype:'Calculation', label:'Sum_Max_Min', sub:'Aggregate per unit', x:600, y:60, tag:'calc'},
        {id:'n4', ptype:'Annotation Query', label:'AnnotationQuery', sub:'Pull operator annotations', x:600, y:290, tag:'calc'},
        {id:'n5', ptype:'Report', label:'WeeklyReport', sub:'Compile PDF and email', x:880, y:170, tag:'action'}
      ],
      edges:[
        {from:'n1',to:'n2'},{from:'n2',to:'n3'},{from:'n2',to:'n4'},{from:'n3',to:'n5'},{from:'n4',to:'n5'}
      ],
      results:{
        n1:{status:'success', out:'[1,540 rows] filtered'},
        n2:{status:'success', out:'6 columns selected'},
        n3:{status:'success', out:'{avg:44.1, max:52.3, min:38.9}'},
        n4:{status:'success', out:'[9] operator notes attached'},
        n5:{status:'success', out:'weekly_report_09-19.pdf sent'}
      }
    },
    demo3:{
      label:'Demo 3', title:'Attribute Audit Flow',
      desc:'Audits tagged sensor attributes, flags anything out of spec, and logs it as a reviewable annotation.',
      nodes:[
        {id:'n1', ptype:'SQL Query', label:'SQLQueries', sub:'Load latest attribute set', x:60, y:170, tag:'sql'},
        {id:'n2', ptype:'Attribute Selector', label:'AttributeSelector', sub:'Filter to out-of-spec tags', x:330, y:170, tag:'bool'},
        {id:'n3', ptype:'Create Annotation', label:'CreateAnnotation', sub:'Draft a review annotation', x:600, y:80, tag:'action'},
        {id:'n4', ptype:'Annotation Update', label:'AnnotationUpdate', sub:'Attach to audit trail', x:600, y:290, tag:'action'},
        {id:'n5', ptype:'Report', label:'AuditWorkflow', sub:'Route to compliance queue', x:880, y:170, tag:'action'}
      ],
      edges:[
        {from:'n1',to:'n2'},{from:'n2',to:'n3'},{from:'n2',to:'n4'},{from:'n3',to:'n5'},{from:'n4',to:'n5'}
      ],
      results:{
        n1:{status:'success', out:'[87] attribute rows'},
        n2:{status:'success', out:'[4] out of spec'},
        n3:{status:'success', out:'annotation_id: AN-1188'},
        n4:{status:'success', out:'audit_trail updated'},
        n5:{status:'success', out:'routed to compliance'}
      }
    }
  };

  const CAT_ICON = {data:'db', logic:'split', actions:'bell', advanced:'code'};

  /* ---------------- State ---------------- */
  let state = { demoId:'demo1', nodes:[], edges:[], selected:null };
  let undoStack = [], redoStack = [];
  let history = [];
  let zoom = 1;
  let settings = { snap:false, minimap:true };
  let closedGroups = new Set();
  let runToken = 0;
  let connecting = null;
  let hoverTarget = null;

  const $ = id => document.getElementById(id);

  function cloneDemo(id){
    const d = DEMOS[id];
    return {
      demoId:id,
      nodes: JSON.parse(JSON.stringify(d.nodes)),
      edges: JSON.parse(JSON.stringify(d.edges)),
      selected:null
    };
  }

  function snapshot(){ return JSON.parse(JSON.stringify(state)); }
  function pushUndo(){ undoStack.push(snapshot()); if(undoStack.length>60) undoStack.shift(); redoStack=[]; updateUndoBtns(); }
  function updateUndoBtns(){ $('undoBtn').disabled = undoStack.length===0; $('redoBtn').disabled = redoStack.length===0; }

  function logHistory(text){
    history.unshift({text, time:new Date()});
    if(history.length>25) history.pop();
    renderHistory();
  }
  function timeAgo(d){
    const s = Math.floor((Date.now()-d.getTime())/1000);
    if(s<5) return 'just now';
    if(s<60) return s+'s ago';
    const m = Math.floor(s/60);
    if(m<60) return m+'m ago';
    return Math.floor(m/60)+'h ago';
  }

  /* ---------------- Init ---------------- */
  function loadDemo(id, silent){
    state = cloneDemo(id);
    resetResultStates();
    renderAll();
    if(!silent) logHistory('Loaded ' + DEMOS[id].label + ' — ' + DEMOS[id].title);
    $('demoLabel').textContent = DEMOS[id].label;
    closeDropdowns();
    showTab('properties');
  }

  function resetResultStates(){
    document.querySelectorAll('.node').forEach(n=>n.classList.remove('running','done-success','done-skipped'));
  }

  /* ---------------- Render: palette ---------------- */
  function renderPalette(filter){
    const wrap = $('palette');
    wrap.innerHTML = '';
    const f = (filter||'').trim().toLowerCase();
    PALETTE.forEach(group=>{
      const gEl = document.createElement('div');
      gEl.className = 'pal-group' + (closedGroups.has(group.key) ? ' closed':'');
      const visibleItems = group.items.filter(it => !f || it.type.toLowerCase().includes(f));
      gEl.innerHTML = `
        <div class="pal-group-head" data-g="${group.key}">
          <svg class="chev" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M6 9l6 6 6-6"/></svg>
          <span class="name">${group.name}</span>
          <span class="count">${group.items.length}</span>
        </div>
        <div class="pal-items">
          ${group.items.map(it=>{
            const dim = f && !it.type.toLowerCase().includes(f);
            return `<div class="pal-item${dim?' dimmed':''}" data-type="${it.type}">
              <span class="pal-ic ${group.key}">${ICONS[it.icon]}</span>
              <span>${it.type}</span>
            </div>`;
          }).join('')}
        </div>`;
      wrap.appendChild(gEl);
    });
    wrap.querySelectorAll('.pal-group-head').forEach(h=>{
      h.addEventListener('click', ()=>{
        const key = h.dataset.g;
        if(closedGroups.has(key)) closedGroups.delete(key); else closedGroups.add(key);
        renderPalette($('searchInput').value);
      });
    });
    wrap.querySelectorAll('.pal-item').forEach(it=>{
      it.addEventListener('click', ()=> addNode(it.dataset.type));
    });
  }

  function addNode(ptype){
    pushUndo();
    const id = 'u' + Math.random().toString(36).slice(2,8);
    const offset = state.nodes.length * 14 % 140;
    state.nodes.push({
      id, ptype,
      label: ptype,
      sub: PALETTE.flatMap(g=>g.items).find(i=>i.type===ptype).sub,
      x: 140 + offset, y: 380 + offset, tag:'new'
    });
    state.selected = id;
    logHistory('Added node "' + ptype + '"');
    renderAll();
    showTab('properties');
  }

  /* ---------------- Render: nodes + edges ---------------- */
  function catOf(ptype){ return TYPE_CATEGORY[ptype] || 'advanced'; }

  function renderNodes(){
    const layer = $('nodesLayer');
    layer.innerHTML = '';
    $('emptyHint').style.display = state.nodes.length ? 'none' : 'block';
    state.nodes.forEach(n=>{
      const cat = catOf(n.ptype);
      const el = document.createElement('div');
      el.className = 'node' + (state.selected===n.id ? ' selected':'');
      el.style.left = n.x+'px'; el.style.top = n.y+'px';
      el.dataset.id = n.id;
      el.innerHTML = `
        <div class="node-port in"></div>
        <div class="node-port out"></div>
        <div class="node-del" data-del="${n.id}">${ICONS.trash}</div>
        <div class="node-head ${cat}">${ICONS[TYPE_ICON[n.ptype] || CAT_ICON[cat]]}<span>${n.label}</span></div>
        <div class="node-body">
          <p class="sub">${n.sub||''}</p>
          <span class="node-tag">${n.ptype}</span>
        </div>`;
      layer.appendChild(el);
      makeDraggable(el, n);
      el.querySelector('[data-del]').addEventListener('click', (e)=>{ e.stopPropagation(); deleteNode(n.id); });
      el.addEventListener('mousedown', ()=> selectNode(n.id));

      const outPort = el.querySelector('.node-port.out');
      const inPort = el.querySelector('.node-port.in');
      outPort.addEventListener('mousedown', (e)=>{
        e.stopPropagation(); e.preventDefault();
        startConnect(n.id, e.clientX, e.clientY);
      });
      inPort.addEventListener('mouseenter', ()=>{
        if(!connecting || connecting.from===n.id) return;
        hoverTarget = n.id;
        inPort.classList.add('port-target');
      });
      inPort.addEventListener('mouseleave', ()=>{
        hoverTarget = (hoverTarget===n.id) ? null : hoverTarget;
        inPort.classList.remove('port-target');
      });
    });
  }

  function selectNode(id){
    if(state.selected===id) return;
    state.selected = id;
    document.querySelectorAll('.node').forEach(el=> el.classList.toggle('selected', el.dataset.id===id));
    renderProperties();
    showTab('properties');
  }

  function deleteNode(id){
    pushUndo();
    const n = state.nodes.find(x=>x.id===id);
    state.nodes = state.nodes.filter(x=>x.id!==id);
    state.edges = state.edges.filter(e=>e.from!==id && e.to!==id);
    if(state.selected===id) state.selected=null;
    logHistory('Deleted node "' + (n?n.label:id) + '"');
    renderAll();
  }

  function makeDraggable(el, n){
    let dragging=false, sx=0, sy=0, ox=0, oy=0, moved=false;
    el.addEventListener('mousedown', (e)=>{
      if(e.target.closest('[data-del]')) return;
      dragging=true; moved=false;
      sx=e.clientX; sy=e.clientY; ox=n.x; oy=n.y;
      e.preventDefault();
    });
    window.addEventListener('mousemove', (e)=>{
      if(!dragging) return;
      const dx = (e.clientX-sx)/zoom, dy=(e.clientY-sy)/zoom;
      if(Math.abs(dx)>2||Math.abs(dy)>2) moved=true;
      let nx = ox+dx, ny = oy+dy;
      if(settings.snap){ nx = Math.round(nx/20)*20; ny=Math.round(ny/20)*20; }
      n.x = Math.max(0,nx); n.y = Math.max(0,ny);
      el.style.left = n.x+'px'; el.style.top = n.y+'px';
      renderEdges(); renderMinimap();
    });
    window.addEventListener('mouseup', ()=>{
      if(dragging && moved) { pushUndo(); }
      dragging=false;
    });
  }

  function portColor(port){
    if(port==='true') return '#22C55E';
    if(port==='false') return '#F04747';
    return 'rgba(255,255,255,0.28)';
  }

  function renderEdges(){
    const svg = $('edgesLayer');
    svg.innerHTML = '';
    state.edges.forEach((e,idx)=>{
      const from = state.nodes.find(n=>n.id===e.from);
      const to = state.nodes.find(n=>n.id===e.to);
      if(!from||!to) return;
      const x1 = from.x+216, y1 = from.y+34;
      const x2 = to.x, y2 = to.y+34;
      const dx = Math.max(60, (x2-x1)*0.5);
      const d = `M ${x1} ${y1} C ${x1+dx} ${y1}, ${x2-dx} ${y2}, ${x2} ${y2}`;

      const hit = document.createElementNS('http://www.w3.org/2000/svg','path');
      hit.setAttribute('d', d);
      hit.setAttribute('class', 'edge-hit');
      hit.setAttribute('title', 'Click to remove this connection');
      hit.addEventListener('click', ()=> deleteEdge(idx));
      svg.appendChild(hit);

      const path = document.createElementNS('http://www.w3.org/2000/svg','path');
      path.setAttribute('d', d);
      path.setAttribute('class', 'edge-path');
      path.setAttribute('stroke', portColor(e.port));
      svg.appendChild(path);
    });
  }

  function deleteEdge(idx){
    const e = state.edges[idx];
    if(!e) return;
    pushUndo();
    const fn = state.nodes.find(n=>n.id===e.from), tn = state.nodes.find(n=>n.id===e.to);
    state.edges.splice(idx,1);
    logHistory('Removed connection "'+(fn?fn.label:e.from)+'" \u2192 "'+(tn?tn.label:e.to)+'"');
    renderAll();
  }

  /* ---------------- Drag-to-connect ports ---------------- */
  function clientToCanvas(clientX, clientY){
    const rect = $('canvasInner').getBoundingClientRect();
    return { x:(clientX-rect.left)/zoom, y:(clientY-rect.top)/zoom };
  }

  function startConnect(fromId, clientX, clientY){
    connecting = { from: fromId };
    hoverTarget = null;
    document.body.style.cursor = 'crosshair';
    updateTempEdge(clientX, clientY);
  }

  function updateTempEdge(clientX, clientY){
    if(!connecting) return;
    const from = state.nodes.find(n=>n.id===connecting.from);
    if(!from) return;
    const p = clientToCanvas(clientX, clientY);
    const x1 = from.x+216, y1 = from.y+34;
    const x2 = p.x, y2 = p.y;
    const dx = Math.max(60, (x2-x1)*0.5);
    const d = `M ${x1} ${y1} C ${x1+dx} ${y1}, ${x2-dx} ${y2}, ${x2} ${y2}`;
    let temp = document.getElementById('tempEdge');
    if(!temp){
      temp = document.createElementNS('http://www.w3.org/2000/svg','path');
      temp.setAttribute('id','tempEdge');
      temp.setAttribute('class','edge-path temp-edge');
      $('edgesLayer').appendChild(temp);
    }
    temp.setAttribute('d', d);
  }

  function endConnect(){
    if(!connecting) return;
    document.body.style.cursor = '';
    const temp = document.getElementById('tempEdge');
    if(temp) temp.remove();
    const fromId = connecting.from;
    const toId = hoverTarget;
    connecting = null;
    document.querySelectorAll('.node-port.port-target').forEach(p=>p.classList.remove('port-target'));
    hoverTarget = null;
    if(toId && toId!==fromId){
      const exists = state.edges.some(e=>e.from===fromId && e.to===toId);
      if(!exists){
        pushUndo();
        state.edges.push({from:fromId, to:toId});
        const fn = state.nodes.find(n=>n.id===fromId), tn = state.nodes.find(n=>n.id===toId);
        logHistory('Connected "'+(fn?fn.label:fromId)+'" \u2192 "'+(tn?tn.label:toId)+'"');
        renderAll();
        return;
      }
    }
    renderEdges();
  }

  window.addEventListener('mousemove', (e)=>{ if(connecting) updateTempEdge(e.clientX, e.clientY); });
  window.addEventListener('mouseup', ()=>{ if(connecting) endConnect(); });

  function renderMinimap(){
    const mm = $('minimap');
    mm.style.display = settings.minimap ? 'block':'none';
    if(!settings.minimap) return;
    mm.innerHTML = '';
    if(!state.nodes.length) return;
    const xs = state.nodes.map(n=>n.x), ys = state.nodes.map(n=>n.y);
    const minX = Math.min(...xs), maxX = Math.max(...xs)+216;
    const minY = Math.min(...ys), maxY = Math.max(...ys)+70;
    const w = Math.max(1,maxX-minX), h = Math.max(1,maxY-minY);
    const pad = 10;
    state.nodes.forEach(n=>{
      const cat = catOf(n.ptype);
      const el = document.createElement('div');
      el.className = 'mm-node';
      const mx = pad + ((n.x-minX)/w) * (200-pad*2-16);
      const my = pad + ((n.y-minY)/h) * (120-pad*2-8);
      el.style.left = mx+'px'; el.style.top = my+'px';
      el.style.width='16px'; el.style.height='8px';
      el.style.background = getComputedStyle(document.documentElement).getPropertyValue('--'+cat);
      mm.appendChild(el);
    });
  }

  function renderStats(){
    $('nodeCount').textContent = state.nodes.length;
    $('edgeCount').textContent = state.edges.length;
  }

  function renderAll(){
    renderNodes();
    renderEdges();
    renderMinimap();
    renderStats();
    renderProperties();
  }

  /* ---------------- Bottom panel: properties ---------------- */
  function renderProperties(){
    if($('tabProperties') === null) return;
    if(document.querySelector('.bp-tab.active')?.dataset.tab !== 'properties') return;
    const c = $('bpContent');
    const n = state.nodes.find(x=>x.id===state.selected);
    if(!n){
      c.innerHTML = `<div class="bp-empty">${ICONS.split.replace('width="24" height="24"','')} Select a node on the canvas to view and edit its properties.</div>`;
      return;
    }
    const cat = catOf(n.ptype);
    c.innerHTML = `
      <div class="prop-grid">
        <div class="prop-row"><label>Label</label><input id="propLabel" value="${escapeAttr(n.label)}"></div>
        <div class="prop-row"><label>Description</label><input id="propSub" value="${escapeAttr(n.sub||'')}"></div>
        <div class="prop-meta">
          <span>Type: <b>${n.ptype}</b></span>
          <span>Category: <b style="text-transform:capitalize">${cat}</b></span>
          <span>ID: <b>${n.id}</b></span>
        </div>
      </div>`;
    $('propLabel').addEventListener('input', e=>{ n.label=e.target.value; renderNodes(); });
    $('propSub').addEventListener('input', e=>{ n.sub=e.target.value; renderNodes(); });
    $('propLabel').addEventListener('change', ()=> pushUndo());
    $('propSub').addEventListener('change', ()=> pushUndo());
  }

  function escapeAttr(s){ return String(s).replace(/"/g,'&quot;'); }

  /* ---------------- Run ---------------- */
  function topoOrder(){
    const ids = state.nodes.map(n=>n.id);
    const indeg = {}; ids.forEach(id=>indeg[id]=0);
    state.edges.forEach(e=>{ if(indeg[e.to]!==undefined) indeg[e.to]++; });
    let queue = ids.filter(id=>indeg[id]===0);
    const order = [];
    const indegCopy = {...indeg};
    const visited = new Set();
    while(queue.length){
      const id = queue.shift();
      if(visited.has(id)) continue;
      visited.add(id); order.push(id);
      state.edges.filter(e=>e.from===id).forEach(e=>{
        indegCopy[e.to]--;
        if(indegCopy[e.to]<=0 && !visited.has(e.to)) queue.push(e.to);
      });
    }
    ids.forEach(id=>{ if(!visited.has(id)) order.push(id); });
    return order;
  }

  function runFlow(){
    if(!state.nodes.length) return;
    const btn = $('runBtn');
    btn.classList.add('running');
    resetResultStates();
    $('runDot').style.display='inline-block';
    showTab('run');
    const c = $('bpContent');
    c.innerHTML = `<div class="results-list" id="resultsList"></div>`;
    const list = $('resultsList');
    const order = topoOrder();
    const demoResults = DEMOS[state.demoId] ? DEMOS[state.demoId].results : {};
    const myToken = ++runToken;

    order.forEach((id, idx)=>{
      setTimeout(()=>{
        if(myToken!==runToken) return;
        const nodeEl = document.querySelector('.node[data-id="'+id+'"]');
        const n = state.nodes.find(x=>x.id===id);
        if(!n) return;
        if(nodeEl) nodeEl.classList.add('running');
        setTimeout(()=>{
          if(myToken!==runToken) return;
          const res = demoResults[id] || {status:'success', out:'executed — no output data'};
          if(nodeEl){
            nodeEl.classList.remove('running');
            nodeEl.classList.add(res.status==='skipped' ? 'done-skipped' : 'done-success');
          }
          const row = document.createElement('div');
          row.className='res-row';
          row.innerHTML = `
            <div class="res-status ${res.status}">${res.status==='skipped'?ICONS.skip:ICONS.check}</div>
            <div class="res-name">${n.label}</div>
            <div class="res-out">${res.out}</div>
            <div class="res-pill ${res.status}">${res.status}</div>`;
          list.appendChild(row);
          if(idx===order.length-1){
            btn.classList.remove('running');
            const banner = document.createElement('div');
            banner.className='run-complete-banner';
            banner.innerHTML = ICONS.check + '<span>Run completed — ' + order.length + ' nodes executed</span>';
            c.appendChild(banner);
            logHistory('Ran flow "' + DEMOS[state.demoId].title + '" (' + order.length + ' nodes)');
          }
        }, 420);
      }, idx*520);
    });
  }

  /* ---------------- Tabs ---------------- */
  function showTab(tab){
    document.querySelectorAll('.bp-tab').forEach(t=> t.classList.toggle('active', t.dataset.tab===tab));
    if($('bottomPanel').classList.contains('collapsed')) $('bottomPanel').classList.remove('collapsed');
    if(tab==='properties') renderProperties();
  }
  $('tabProperties').addEventListener('click', ()=> showTab('properties'));
  $('tabRun').addEventListener('click', ()=>{
    showTab('run');
    if(!$('resultsList')){
      $('bpContent').innerHTML = `<div class="bp-empty">${ICONS.check.replace('width="24" height="24"','')} Click Run to execute the current flow and see per-node output here.</div>`;
    }
  });

  $('bpToggle').addEventListener('click', ()=> $('bottomPanel').classList.toggle('collapsed'));

  /* ---------------- History panel ---------------- */
  function renderHistory(){
    const l = $('histList');
    if(!history.length){ l.innerHTML = '<div class="hist-empty">No activity yet</div>'; return; }
    l.innerHTML = history.map(h=>`<div class="hist-row"><span class="t">${h.text}</span><span class="s">${timeAgo(h.time)}</span></div>`).join('');
  }
  setInterval(renderHistory, 15000);

  /* ---------------- Dropdown / popover plumbing ---------------- */
  function closeDropdowns(){
    document.querySelectorAll('.dropdown.open, .popover.open').forEach(d=>d.classList.remove('open'));
  }
  function toggleEl(el){
    const wasOpen = el.classList.contains('open');
    closeDropdowns();
    if(!wasOpen) el.classList.add('open');
  }
  $('fileBtn').addEventListener('click', e=>{ e.stopPropagation(); toggleEl($('fileMenu')); });
  $('demoBtn').addEventListener('click', e=>{ e.stopPropagation(); toggleEl($('demoMenu')); });
  $('histBtn').addEventListener('click', e=>{ e.stopPropagation(); toggleEl($('histPanel')); });
  $('setBtn').addEventListener('click', e=>{ e.stopPropagation(); toggleEl($('setPanel')); });
  document.addEventListener('click', closeDropdowns);
  document.querySelectorAll('.dropdown, .popover').forEach(d=> d.addEventListener('click', e=>e.stopPropagation()));

  $('demoMenu').innerHTML = Object.keys(DEMOS).map(id=>{
    const d = DEMOS[id];
    return `<button data-demo="${id}"><span>${d.label} — ${d.title}</span></button>`;
  }).join('') + '<div class="dd-sep"></div><button data-demo="__blank">Blank canvas</button>';
  $('demoMenu').querySelectorAll('button').forEach(b=>{
    b.addEventListener('click', ()=>{
      const id = b.dataset.demo;
      if(id==='__blank'){
        pushUndo();
        state = { demoId:null, nodes:[], edges:[], selected:null };
        renderAll();
        $('demoLabel').textContent = 'Blank';
        logHistory('Started a blank canvas');
        closeDropdowns();
      } else {
        loadDemo(id);
      }
    });
  });

  $('fileMenu').querySelectorAll('button').forEach(b=>{
    b.addEventListener('click', ()=>{
      const act = b.dataset.act;
      if(act==='new'){
        pushUndo();
        state = { demoId:null, nodes:[], edges:[], selected:null };
        renderAll();
        $('demoLabel').textContent = 'Blank';
        logHistory('Started a new empty flow');
      } else if(act==='reset'){
        if(state.demoId) loadDemo(state.demoId);
      } else if(act==='export'){
        const data = JSON.stringify({ name:$('treeName').value, nodes:state.nodes, edges:state.edges }, null, 2);
        const blob = new Blob([data], {type:'application/json'});
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url; a.download = (($('treeName').value||'decision-tree').replace(/\\s+/g,'-').toLowerCase())+'.json';
        a.click();
        URL.revokeObjectURL(url);
        logHistory('Exported flow as JSON');
      }
      closeDropdowns();
    });
  });

  /* ---------------- Undo / redo ---------------- */
  $('undoBtn').addEventListener('click', ()=>{
    if(!undoStack.length) return;
    redoStack.push(snapshot());
    state = undoStack.pop();
    renderAll();
    updateUndoBtns();
  });
  $('redoBtn').addEventListener('click', ()=>{
    if(!redoStack.length) return;
    undoStack.push(snapshot());
    state = redoStack.pop();
    renderAll();
    updateUndoBtns();
  });

  /* ---------------- Search ---------------- */
  $('searchInput').addEventListener('input', e=> renderPalette(e.target.value));

  /* ---------------- Settings ---------------- */
  $('snapToggle').addEventListener('change', e=> settings.snap = e.target.checked);
  $('mmToggle').addEventListener('change', e=>{ settings.minimap = e.target.checked; renderMinimap(); });

  /* ---------------- Sidebar collapse ---------------- */
  $('collapseBtn').addEventListener('click', ()=> $('sidebar').classList.toggle('collapsed'));

  /* ---------------- Zoom ---------------- */
  function applyZoom(){
    $('canvasInner').style.transform = 'scale('+zoom+')';
    $('zoomPct').textContent = Math.round(zoom*100)+'%';
  }
  $('zoomIn').addEventListener('click', ()=>{ zoom=Math.min(1.6, zoom+0.1); applyZoom(); });
  $('zoomOut').addEventListener('click', ()=>{ zoom=Math.max(0.4, zoom-0.1); applyZoom(); });
  $('zoomFit').addEventListener('click', ()=>{ zoom=1; applyZoom(); $('canvasOuter').scrollTo(0,0); });

  /* ---------------- Run button ---------------- */
  $('runBtn').addEventListener('click', runFlow);

  /* ---------------- Canvas background click deselects ---------------- */
  $('canvasOuter').addEventListener('mousedown', (e)=>{
    if(e.target.id==='canvasOuter' || e.target.id==='canvasInner' || e.target.id==='nodesLayer'){
      state.selected = null;
      document.querySelectorAll('.node').forEach(el=>el.classList.remove('selected'));
      renderProperties();
    }
  });

  /* ---------------- Keyboard ---------------- */
  document.addEventListener('keydown', (e)=>{
    if((e.key==='Delete'||e.key==='Backspace') && state.selected && document.activeElement.tagName!=='INPUT'){
      deleteNode(state.selected);
    }
    if(e.key==='Escape') closeDropdowns();
  });

  /* ---------------- Boot ---------------- */
  renderPalette('');
  loadDemo('demo1', true);
  applyZoom();
  logHistory('Opened prototype — loaded Demo 1');
  updateUndoBtns();

})();
</script>
</body>
</html>
