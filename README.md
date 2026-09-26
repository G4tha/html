<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="color-scheme" content="dark">
<title>Editor HTML/CSS/JS</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/codemirror.min.css">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/theme/material-darker.min.css">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/dialog/dialog.min.css">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/hint/show-hint.min.css">
<style>
:root{
  --bg:#0b0e12;
  --panel:#11151a;
  --panel-2:#171c22;
  --panel-3:#1e242c;
  --border:#252c35;
  --border-2:#333c47;
  --fg:#d5dbe3;
  --fg-dim:#828d9b;
  --fg-mute:#5b6572;
  --accent:#4c9aff;
  --accent-soft:#1d3category;
  --danger:#e5534b;
  --warn:#d9a441;
  --ok:#4caf7d;
  --mono:ui-monospace,SFMono-Regular,"SF Mono",Menlo,Consolas,"Liberation Mono",monospace;
  --ui:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,sans-serif;
  --code-fs:13px;
  --kb-offset:0px;
}
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent;}
html,body{margin:0;padding:0;}
body{
  height:100vh;height:100dvh;
  display:flex;flex-direction:column;
  background:var(--bg);color:var(--fg);
  font-family:var(--ui);font-size:14px;line-height:1.4;
  overflow:hidden;
  -webkit-text-size-adjust:100%;
}
button{font-family:inherit;font-size:inherit;color:inherit;background:none;border:0;padding:0;cursor:pointer;}
input{font-family:inherit;font-size:inherit;}
.icon{width:20px;height:20px;fill:none;stroke:currentColor;stroke-width:1.7;stroke-linecap:round;stroke-linejoin:round;display:block;flex:0 0 auto;}
.icon.sm{width:16px;height:16px;}

/* ---------- APP BAR ---------- */
.appbar{
  flex:0 0 auto;display:flex;align-items:center;gap:4px;
  height:50px;padding:0 6px;
  background:var(--panel);border-bottom:1px solid var(--border);
  padding-top:env(safe-area-inset-top);
}
.ibtn{
  display:inline-flex;align-items:center;justify-content:center;
  min-width:44px;height:44px;border-radius:7px;color:var(--fg-dim);
}
.ibtn:hover{background:var(--panel-3);color:var(--fg);}
.ibtn.sm{min-width:36px;height:36px;border-radius:6px;}
.ibtn.on{background:var(--panel-3);color:var(--accent);}
.pmeta{flex:1 1 auto;min-width:0;display:flex;flex-direction:column;justify-content:center;padding:0 2px;}
.pname{
  display:block;max-width:100%;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;
  text-align:left;font-size:14px;font-weight:600;color:var(--fg);padding:2px 4px;border-radius:5px;
}
.pname:hover{background:var(--panel-3);}
.save-ind{font-size:10.5px;color:var(--fg-mute);padding-left:4px;height:13px;}
.save-ind.unsaved{color:var(--warn);}
.runbtn{
  display:inline-flex;align-items:center;gap:6px;
  height:40px;min-width:80px;justify-content:center;
  padding:0 14px;border-radius:8px;
  background:var(--accent);color:#04101f;font-weight:700;font-size:14px;
}
.runbtn:active{background:#3b82e0;}
.runbtn .icon{stroke-width:2.2;}

/* ---------- VIEW NAV ---------- */
.viewnav{
  flex:0 0 auto;display:flex;align-items:stretch;height:40px;
  background:var(--bg);border-bottom:1px solid var(--border);
}
.viewnav button{
  flex:1 1 0;display:inline-flex;align-items:center;justify-content:center;gap:6px;
  font-size:13px;color:var(--fg-dim);border-bottom:2px solid transparent;
}
.viewnav button.active{color:var(--fg);border-bottom-color:var(--accent);}
.badge{
  display:none;min-width:16px;height:16px;padding:0 4px;border-radius:8px;
  background:var(--danger);color:#fff;font-size:10px;font-weight:700;
  align-items:center;justify-content:center;line-height:1;
}
.badge.show{display:inline-flex;}

/* ---------- WORKSPACE ---------- */
.workspace{flex:1 1 auto;position:relative;min-height:0;display:flex;flex-direction:column;}
.view{display:none;flex:1 1 auto;min-height:0;flex-direction:column;}
.workspace[data-view="code"] #view-code{display:flex;}
.workspace[data-view="preview"] #view-preview{display:flex;}
.workspace[data-view="console"] #view-console{display:flex;}

/* ---------- EDITOR ---------- */
.editorbar{
  flex:0 0 auto;display:flex;align-items:stretch;height:46px;
  background:var(--panel);border-bottom:1px solid var(--border);
}
.etabs{display:flex;flex:0 0 auto;}
.etabs button{
  position:relative;min-width:56px;padding:0 12px;font-size:12.5px;font-weight:600;
  letter-spacing:.4px;color:var(--fg-dim);border-bottom:2px solid transparent;
}
.etabs button.active{color:var(--fg);border-bottom-color:var(--accent);background:var(--panel-2);}
.etabs .dot{display:none;position:absolute;top:9px;right:8px;width:6px;height:6px;border-radius:50%;background:var(--accent);}
.etabs button.stale .dot{display:block;}
.eacts{
  flex:1 1 auto;display:flex;align-items:center;justify-content:flex-end;
  overflow-x:auto;overflow-y:hidden;scrollbar-width:none;
}
.eacts::-webkit-scrollbar{display:none;}
.eacts button{
  flex:0 0 auto;height:44px;padding:0 11px;font-size:12px;color:var(--fg-dim);
  display:inline-flex;align-items:center;gap:5px;white-space:nowrap;
}
.eacts button:hover{color:var(--fg);}
.eacts button.mono{font-family:var(--mono);font-size:12.5px;}

.editors{flex:1 1 auto;position:relative;min-height:0;background:var(--bg);}
.epane{position:absolute;inset:0;display:none;}
.epane.active{display:block;}

.CodeMirror{height:100%;font-family:var(--mono);font-size:var(--code-fs);line-height:1.55;background:var(--bg);}
.cm-s-material-darker.CodeMirror{background:var(--bg);color:#cfd6de;}
.cm-s-material-darker .CodeMirror-gutters{background:var(--bg);border-right:1px solid #1b2129;}
.cm-s-material-darker .CodeMirror-linenumber{color:#454e59;font-size:11px;}
.cm-s-material-darker .CodeMirror-cursor{border-left:2px solid var(--accent);}
.cm-s-material-darker .CodeMirror-selected{background:#1f2c3d;}
.cm-s-material-darker .CodeMirror-line::selection,.cm-s-material-darker .CodeMirror-line>span::selection{background:#1f2c3d;}
.cm-s-material-darker .CodeMirror-activeline-background{background:#141a21;}
.cm-s-material-darker .CodeMirror-matchingbracket{color:#8fd0ff !important;border-bottom:1px solid #8fd0ff;}
.cm-s-material-darker .cm-tag,.cm-s-material-darker .cm-keyword{color:#7aa7e8;}
.cm-s-material-darker .cm-attribute,.cm-s-material-darker .cm-def{color:#d4b26a;}
.cm-s-material-darker .cm-string,.cm-s-material-darker .cm-string-2{color:#8ec98e;}
.cm-s-material-darker .cm-number,.cm-s-material-darker .cm-atom{color:#d9955f;}
.cm-s-material-darker .cm-comment{color:#5b6572;font-style:italic;}
.cm-s-material-darker .cm-property{color:#7aa7e8;}
.cm-s-material-darker .cm-qualifier,.cm-s-material-darker .cm-builtin{color:#d4b26a;}
.cm-s-material-darker .cm-variable,.cm-s-material-darker .cm-variable-3{color:#cfd6de;}
.cm-s-material-darker .cm-variable-2{color:#8fd0c0;}
.cm-s-material-darker .cm-operator,.cm-s-material-darker .cm-bracket{color:#98a4b2;}
.cm-s-material-darker .cm-error{color:#ff8b82;}
.CodeMirror-hints{
  background:var(--panel-2);border:1px solid var(--border-2);border-radius:8px;
  box-shadow:0 8px 22px rgba(0,0,0,.5);font-family:var(--mono);font-size:13px;z-index:60;
}
.CodeMirror-hint{color:var(--fg);padding:6px 10px;border-radius:5px;}
li.CodeMirror-hint-active{background:var(--accent);color:#04101f;}
.CodeMirror-dialog{background:var(--panel-2);border-bottom:1px solid var(--border-2);color:var(--fg);padding:6px 8px;font-family:var(--ui);font-size:13px;}
.CodeMirror-dialog input{background:var(--bg);border:1px solid var(--border-2);color:var(--fg);padding:5px 7px;border-radius:5px;outline:none;}
.CodeMirror-dialog button{color:var(--fg-dim);padding:4px 8px;}

.statusbar{
  flex:0 0 auto;display:flex;gap:12px;align-items:center;height:26px;padding:0 10px;
  background:var(--panel);border-top:1px solid var(--border);
  font-size:11px;color:var(--fg-mute);font-family:var(--mono);
  overflow:hidden;white-space:nowrap;
}
.statusbar span:last-child{margin-left:auto;color:var(--fg-dim);}

/* ---------- PREVIEW ---------- */
.pbar{
  flex:0 0 auto;display:flex;align-items:center;gap:8px;height:40px;padding:0 8px 0 12px;
  background:var(--panel);border-bottom:1px solid var(--border);
}
.pbar-title{font-size:12.5px;font-weight:600;}
.pbar-note{font-size:10.5px;color:var(--fg-mute);font-family:var(--mono);margin-right:auto;}
.frame-wrap{flex:1 1 auto;position:relative;min-height:0;background:#fff;}
#preview{width:100%;height:100%;border:0;display:block;background:#fff;}
.pnote{
  flex:0 0 auto;margin:0;padding:8px 12px;font-size:11px;line-height:1.5;color:var(--fg-mute);
  background:var(--panel);border-top:1px solid var(--border);
}
.pnote code{font-family:var(--mono);color:var(--fg-dim);}
#view-preview.fs{position:fixed;inset:0;z-index:90;background:var(--bg);}
#view-preview.fs .pnote{display:none;}

/* ---------- CONSOLE ---------- */
.cbar{
  flex:0 0 auto;display:flex;align-items:center;gap:8px;height:44px;padding:0 8px;
  background:var(--panel);border-bottom:1px solid var(--border);
}
.seg{display:flex;background:var(--bg);border:1px solid var(--border);border-radius:7px;overflow:hidden;}
.seg button{
  display:inline-flex;align-items:center;gap:6px;
  padding:0 12px;height:32px;font-size:12px;color:var(--fg-dim);
}
.seg button.active{background:var(--panel-3);color:var(--fg);}
.cbar .ibtn{margin-left:auto;}
.clog{
  flex:1 1 auto;min-height:0;overflow-y:auto;overflow-x:hidden;
  padding:6px 0;font-family:var(--mono);font-size:12.5px;
  -webkit-overflow-scrolling:touch;
}
.clog[hidden]{display:none;}
.log{padding:5px 12px;border-bottom:1px solid #151a20;word-break:break-word;white-space:pre-wrap;}
.log-text{color:var(--fg);}
.log-log .log-text{color:#bcc6d2;}
.log-warn{border-left:3px solid var(--warn);background:#1a1710;}
.log-warn .log-text{color:#e3c47f;}
.log-error{border-left:3px solid var(--danger);background:#1a1112;}
.log-error .log-text{color:#f0928c;}
.log-info .log-text{color:#8fb6e0;}
.log-jump{
  display:inline-block;margin-top:5px;padding:4px 9px;border-radius:5px;
  background:var(--panel-3);color:var(--accent);font-size:11.5px;border:1px solid var(--border-2);
}
.clog-empty{padding:22px 14px;color:var(--fg-mute);font-family:var(--ui);font-size:12.5px;text-align:center;}

/* ---------- SHORTCUT BAR ---------- */
.shortcutbar{
  position:fixed;left:0;right:0;bottom:0;z-index:50;
  display:none;gap:6px;align-items:center;
  padding:6px 8px calc(6px + env(safe-area-inset-bottom));
  background:var(--panel-2);border-top:1px solid var(--border-2);
  overflow-x:auto;overflow-y:hidden;white-space:nowrap;
  scrollbar-width:none;touch-action:pan-x;
  transform:translateY(calc(-1 * var(--kb-offset)));
  transition:transform .18s ease-out;
}
.shortcutbar.show{display:flex;}
.shortcutbar::-webkit-scrollbar{display:none;}
.sk{
  flex:0 0 auto;min-width:44px;height:44px;padding:0 10px;
  display:inline-flex;align-items:center;justify-content:center;
  background:var(--panel-3);border:1px solid var(--border-2);border-radius:8px;
  color:var(--fg);font-family:var(--mono);font-size:14px;
}
.sk:active{background:#2a323c;}
.sk.on{background:var(--accent);border-color:var(--accent);color:#04101f;}
.sk.wide{min-width:58px;font-size:12px;font-family:var(--ui);}
.sk .icon{width:18px;height:18px;}
body.kb #view-code{padding-bottom:58px;}

/* ---------- DRAWER ---------- */
.backdrop{
  position:fixed;inset:0;background:rgba(0,0,0,.55);z-index:70;
  opacity:0;pointer-events:none;transition:opacity .16s;
}
.backdrop.show{opacity:1;pointer-events:auto;}
.drawer{
  position:fixed;top:0;bottom:0;left:0;z-index:71;
  width:min(330px,88vw);background:var(--panel);border-right:1px solid var(--border-2);
  display:flex;flex-direction:column;
  transform:translateX(-101%);transition:transform .2s ease-out;
}
.drawer.show{transform:translateX(0);}
.drawer-head{
  flex:0 0 auto;display:flex;align-items:center;height:50px;padding:0 6px 0 14px;
  border-bottom:1px solid var(--border);font-size:13.5px;font-weight:600;
  padding-top:env(safe-area-inset-top);
}
.drawer-head .ibtn{margin-left:auto;}
.drawer-body{flex:1 1 auto;overflow-y:auto;padding-bottom:24px;-webkit-overflow-scrolling:touch;}
.dsec{border-bottom:1px solid var(--border);padding:6px 0 10px;}
.dsec-head{
  display:flex;align-items:center;height:34px;padding:0 8px 0 14px;
  font-size:10.5px;letter-spacing:.9px;text-transform:uppercase;color:var(--fg-mute);font-weight:700;
}
.dsec-head .ibtn{margin-left:auto;}
.list{display:flex;flex-direction:column;}
.frow{display:flex;align-items:center;gap:2px;padding:0 6px;}
.frow.active{background:var(--panel-3);}
.frow-main{
  flex:1 1 auto;min-width:0;display:flex;flex-direction:column;gap:1px;
  text-align:left;padding:9px 8px;border-radius:6px;
}
.frow-name{font-size:13px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;}
.frow-sub{font-size:10.5px;color:var(--fg-mute);}
.ftag{
  flex:0 0 auto;width:44px;font-size:10px;font-weight:700;letter-spacing:.5px;
  color:var(--fg-mute);font-family:var(--mono);
}
.fname{
  flex:1 1 auto;min-width:0;background:var(--bg);border:1px solid var(--border);
  color:var(--fg);border-radius:6px;padding:8px 9px;font-size:12.5px;font-family:var(--mono);
  outline:none;
}
.fname:focus{border-color:var(--accent);}
.toolgrid{display:grid;grid-template-columns:1fr 1fr;gap:8px;padding:4px 14px 0;}
.btn{
  display:inline-flex;align-items:center;justify-content:center;gap:6px;
  min-height:40px;padding:0 12px;border-radius:7px;
  background:var(--panel-3);border:1px solid var(--border-2);color:var(--fg);
  font-size:12.5px;
}
.btn:active{background:#2a323c;}
.btn.primary{background:var(--accent);border-color:var(--accent);color:#04101f;font-weight:700;}
.btn.danger{color:#f0928c;border-color:#4a2a2a;}
.btn.block{width:100%;}

/* ---------- SHEET / MODAL ---------- */
.overlay{
  position:fixed;inset:0;z-index:100;background:rgba(0,0,0,.6);
  display:flex;align-items:flex-end;justify-content:center;
}
.overlay[hidden]{display:none;}
.sheet{
  width:100%;max-width:540px;max-height:86vh;display:flex;flex-direction:column;
  background:var(--panel);border:1px solid var(--border-2);border-bottom:0;
  border-radius:12px 12px 0 0;
}
.sheet-head{
  flex:0 0 auto;display:flex;align-items:center;height:48px;padding:0 6px 0 14px;
  border-bottom:1px solid var(--border);
}
.sheet-head h2{margin:0;font-size:13.5px;font-weight:600;}
.sheet-head .ibtn{margin-left:auto;}
.sheet-body{flex:1 1 auto;overflow-y:auto;padding:12px 14px 18px;-webkit-overflow-scrolling:touch;}
.sheet-msg{margin:0 0 14px;font-size:13px;color:var(--fg-dim);line-height:1.55;}
.sheet-actions{display:flex;gap:8px;}
.sheet-actions .btn{flex:1 1 0;}
.field{display:flex;flex-direction:column;gap:6px;margin-bottom:14px;}
.field label{font-size:11.5px;color:var(--fg-dim);font-weight:600;}
.field input[type=text],.field input[type=number]{
  background:var(--bg);border:1px solid var(--border);color:var(--fg);
  border-radius:7px;padding:10px 11px;font-size:13px;outline:none;width:100%;
}
.field input:focus{border-color:var(--accent);}
.field .row{display:flex;align-items:center;gap:10px;}
.field .row input[type=range]{flex:1 1 auto;accent-color:var(--accent);}
.field .val{font-family:var(--mono);font-size:12px;color:var(--fg-dim);min-width:42px;text-align:right;}
.seg-inline{display:flex;gap:0;border:1px solid var(--border);border-radius:7px;overflow:hidden;width:max-content;}
.seg-inline button{padding:8px 16px;font-size:12.5px;color:var(--fg-dim);background:var(--bg);}
.seg-inline button.active{background:var(--panel-3);color:var(--fg);}
.switch{display:flex;align-items:center;justify-content:space-between;gap:12px;padding:9px 0;border-bottom:1px solid var(--border);}
.switch:last-child{border-bottom:0;}
.switch span{font-size:13px;}
.tog{
  position:relative;width:44px;height:26px;border-radius:13px;background:var(--panel-3);
  border:1px solid var(--border-2);flex:0 0 auto;transition:background .15s;
}
.tog::after{
  content:"";position:absolute;top:2px;left:2px;width:20px;height:20px;border-radius:50%;
  background:var(--fg-mute);transition:transform .15s,background .15s;
}
.tog.on{background:#1d3category;}
.tog.on{background:#1c3category;}
.tog.on{background:#1b3a5e;border-color:var(--accent);}
.tog.on::after{transform:translateX(18px);background:var(--accent);}

.palette-input{
  width:100%;background:var(--bg);border:1px solid var(--border-2);color:var(--fg);
  border-radius:8px;padding:11px 12px;font-size:14px;outline:none;margin-bottom:10px;
}
.palette-input:focus{border-color:var(--accent);}
.cmd-list{display:flex;flex-direction:column;gap:2px;}
.cmd{
  display:flex;align-items:center;justify-content:space-between;gap:10px;
  padding:11px 12px;border-radius:7px;text-align:left;font-size:13px;color:var(--fg);
}
.cmd:hover,.cmd.sel{background:var(--panel-3);}
.cmd small{color:var(--fg-mute);font-family:var(--mono);font-size:10.5px;}
.cmd-list .empty{padding:16px 12px;color:var(--fg-mute);font-size:12.5px;}

/* ---------- TOAST ---------- */
.toast{
  position:fixed;left:50%;bottom:calc(24px + env(safe-area-inset-bottom));
  transform:translateX(-50%) translateY(20px);
  background:var(--panel-3);border:1px solid var(--border-2);color:var(--fg);
  padding:10px 16px;border-radius:8px;font-size:12.5px;z-index:120;
  opacity:0;pointer-events:none;transition:opacity .18s,transform .18s;
  max-width:90vw;text-align:center;
}
.toast.show{opacity:1;transform:translateX(-50%) translateY(0);}

/* ---------- DESKTOP ---------- */
@media (min-width:1000px){
  .viewnav{display:none;}
  .workspace{
    display:grid;
    grid-template-columns:minmax(0,1.05fr) minmax(0,1fr);
    grid-template-rows:minmax(0,1fr) 190px;
    gap:1px;background:var(--border);
  }
  .workspace[data-view] #view-code{display:flex;grid-column:1;grid-row:1 / span 2;}
  .workspace[data-view] #view-preview{display:flex;grid-column:2;grid-row:1;}
  .workspace[data-view] #view-console{display:flex;grid-column:2;grid-row:2;}
  .overlay{align-items:center;}
  .sheet{border-radius:12px;border-bottom:1px solid var(--border-2);}
  .shortcutbar{border-radius:0;}
  body.kb #view-code{padding-bottom:0;}
  .pnote{display:none;}
}
@media (max-width:999px){
  .appbar{padding-top:0;}
}
@media (max-width:380px){
  .etabs button{min-width:48px;padding:0 9px;font-size:12px;}
  .runbtn{min-width:66px;padding:0 11px;}
}
</style>
</head>
<body>

<svg style="display:none" aria-hidden="true" focusable="false" xmlns="http://www.w3.org/2000/svg">
  <symbol id="icon-run" viewBox="0 0 24 24"><path d="M7 4.5l12 7.5-12 7.5z"/></symbol>
  <symbol id="icon-save" viewBox="0 0 24 24"><path d="M5 4h11l3 3v13H5z"/><path d="M8.5 4v6h7V4"/><path d="M8.5 20v-6h7v6"/></symbol>
  <symbol id="icon-files" viewBox="0 0 24 24"><path d="M3 6.5h6l2 2h10v10.5H3z"/></symbol>
  <symbol id="icon-settings" viewBox="0 0 24 24"><circle cx="12" cy="12" r="3.2"/><path d="M12 2.6v2.9M12 18.5v2.9M2.6 12h2.9M18.5 12h2.9M5.3 5.3l2 2M16.7 16.7l2 2M18.7 5.3l-2 2M7.3 16.7l-2 2"/></symbol>
  <symbol id="icon-search" viewBox="0 0 24 24"><circle cx="10.5" cy="10.5" r="6"/><path d="M15.2 15.2L20.5 20.5"/></symbol>
  <symbol id="icon-format" viewBox="0 0 24 24"><path d="M4 6h16M7.5 10.5h9M4 15h16M7.5 19h9"/></symbol>
  <symbol id="icon-copy" viewBox="0 0 24 24"><rect x="9" y="9" width="11" height="11" rx="2"/><path d="M5 15.5V6.5A2 2 0 0 1 7 4.5h9"/></symbol>
  <symbol id="icon-close" viewBox="0 0 24 24"><path d="M6 6l12 12M18 6L6 18"/></symbol>
  <symbol id="icon-chevron-left" viewBox="0 0 24 24"><path d="M15 5l-7 7 7 7"/></symbol>
  <symbol id="icon-chevron-right" viewBox="0 0 24 24"><path d="M9 5l7 7-7 7"/></symbol>
  <symbol id="icon-chevron-up" viewBox="0 0 24 24"><path d="M5 15l7-7 7 7"/></symbol>
  <symbol id="icon-chevron-down" viewBox="0 0 24 24"><path d="M5 9l7 7 7-7"/></symbol>
  <symbol id="icon-terminal" viewBox="0 0 24 24"><rect x="3" y="4.5" width="18" height="15" rx="2"/><path d="M7.5 10l3 2.5-3 2.5M13.5 15h3.5"/></symbol>
  <symbol id="icon-warning" viewBox="0 0 24 24"><path d="M12 4l9 16H3z"/><path d="M12 10v4M12 17.1v.1"/></symbol>
  <symbol id="icon-plus" viewBox="0 0 24 24"><path d="M12 5v14M5 12h14"/></symbol>
  <symbol id="icon-trash" viewBox="0 0 24 24"><path d="M4 7h16M9.5 7V4h5v3M6.5 7l1 13h9l1-13"/></symbol>
  <symbol id="icon-share" viewBox="0 0 24 24"><circle cx="6" cy="12" r="2.5"/><circle cx="18" cy="6" r="2.5"/><circle cx="18" cy="18" r="2.5"/><path d="M8.3 10.8l7.4-3.6M8.3 13.2l7.4 3.6"/></symbol>
  <symbol id="icon-refresh" viewBox="0 0 24 24"><path d="M20 12a8 8 0 1 1-2.4-5.7"/><path d="M20 4v4.2h-4.2"/></symbol>
  <symbol id="icon-expand" viewBox="0 0 24 24"><path d="M4 9.5V4h5.5M20 14.5V20h-5.5M14.5 4H20v5.5M9.5 20H4v-5.5"/></symbol>
  <symbol id="icon-more" viewBox="0 0 24 24"><circle cx="5" cy="12" r="1.7" fill="currentColor" stroke="none"/><circle cx="12" cy="12" r="1.7" fill="currentColor" stroke="none"/><circle cx="19" cy="12" r="1.7" fill="currentColor" stroke="none"/></symbol>
  <symbol id="icon-doc" viewBox="0 0 24 24"><path d="M6 3.5h8l4 4v13H6z"/><path d="M14 3.5v4h4"/></symbol>
</svg>

<header class="appbar">
  <button class="ibtn" id="btnDrawer" aria-label="Berkas dan proyek"><svg class="icon"><use href="#icon-files"></use></svg></button>
  <div class="pmeta">
    <button class="pname" id="btnProjectName">Proyek</button>
    <span class="save-ind" id="saveInd"></span>
  </div>
  <button class="ibtn" id="btnSettings" aria-label="Pengaturan"><svg class="icon"><use href="#icon-settings"></use></svg></button>
  <button class="runbtn" id="btnRun" aria-label="Jalankan kode">
    <svg class="icon"><use href="#icon-run"></use></svg><span>Run</span>
  </button>
</header>

<nav class="viewnav" id="viewNav">
  <button data-view="code" class="active">Code</button>
  <button data-view="preview">Preview</button>
  <button data-view="console">Console <span class="badge" id="errBadge"></span></button>
</nav>

<div class="workspace" id="workspace" data-view="code">

  <section class="view" id="view-code">
    <div class="editorbar">
      <div class="etabs" id="editorTabs">
        <button data-lang="html" class="active">HTML<span class="dot"></span></button>
        <button data-lang="css">CSS<span class="dot"></span></button>
        <button data-lang="js">JS<span class="dot"></span></button>
      </div>
      <div class="eacts" id="editorActs">
        <button id="btnTag" class="mono" title="HTML Shortcut: ubah nama tag jadi tag lengkap">&lt;/&gt;</button>
        <button id="btnEmmet" title="Emmet expand">Emmet</button>
        <button id="btnFormat" title="Format kode"><svg class="icon sm"><use href="#icon-format"></use></svg></button>
        <button id="btnSearch" title="Cari dan ganti"><svg class="icon sm"><use href="#icon-search"></use></svg></button>
        <button id="btnComment" class="mono" title="Komentar / batalkan komentar">//</button>
        <button id="btnCopy" title="Salin semua kode"><svg class="icon sm"><use href="#icon-copy"></use></svg></button>
        <button id="btnLines" title="Operasi baris">Baris</button>
      </div>
    </div>
    <div class="editors" id="editors">
      <div class="epane" data-lang="html"><textarea id="ta-html" spellcheck="false" autocapitalize="off" autocomplete="off"></textarea></div>
      <div class="epane" data-lang="css"><textarea id="ta-css" spellcheck="false" autocapitalize="off" autocomplete="off"></textarea></div>
      <div class="epane" data-lang="js"><textarea id="ta-js" spellcheck="false" autocapitalize="off" autocomplete="off"></textarea></div>
    </div>
    <div class="statusbar">
      <span id="stPos">Baris 1, Kol 1</span>
      <span id="stCount"></span>
      <span id="stLang">HTML</span>
    </div>
  </section>

  <section class="view" id="view-preview">
    <div class="pbar">
      <span class="pbar-title">Preview</span>
      <span class="pbar-note">sandbox="allow-scripts"</span>
      <button class="ibtn sm" id="btnReload" title="Jalankan ulang"><svg class="icon sm"><use href="#icon-refresh"></use></svg></button>
      <button class="ibtn sm" id="btnFs" title="Layar penuh"><svg class="icon sm"><use href="#icon-expand"></use></svg></button>
    </div>
    <div class="frame-wrap" id="frameWrap">
      <iframe id="preview" sandbox="allow-scripts" title="Preview kode pengguna"></iframe>
    </div>
    <p class="pnote">
      Preview berjalan di dalam iframe dengan <code>sandbox="allow-scripts"</code> saja (tanpa <code>allow-same-origin</code>).
      Karena itu kode di dalam preview <strong>tidak bisa</strong> memakai <code>localStorage</code>, cookie, atau <code>fetch</code> ke origin yang sama.
      Ini pilihan sadar demi keamanan, bukan bug.
    </p>
  </section>

  <section class="view" id="view-console">
    <div class="cbar">
      <div class="seg" id="conSeg">
        <button data-panel="log" class="active">Console</button>
        <button data-panel="err">Error <span class="badge" id="errBadge2"></span></button>
      </div>
      <button class="ibtn sm" id="btnClearCon" title="Bersihkan"><svg class="icon sm"><use href="#icon-trash"></use></svg></button>
    </div>
    <div class="clog" id="logList"></div>
    <div class="clog" id="errList" hidden></div>
  </section>

</div>

<div class="shortcutbar" id="shortcutBar" aria-label="Tombol pintasan kode"></div>

<div class="backdrop" id="backdrop"></div>

<aside class="drawer" id="drawer" aria-label="Panel proyek">
  <div class="drawer-head">
    <span>Proyek &amp; Berkas</span>
    <button class="ibtn sm" id="drawerClose" aria-label="Tutup"><svg class="icon sm"><use href="#icon-close"></use></svg></button>
  </div>
  <div class="drawer-body">
    <div class="dsec">
      <div class="dsec-head">
        <span>Proyek</span>
        <button class="ibtn sm" id="btnNewProject" title="Proyek baru"><svg class="icon sm"><use href="#icon-plus"></use></svg></button>
      </div>
      <div class="list" id="projectList"></div>
    </div>
    <div class="dsec">
      <div class="dsec-head"><span>Berkas proyek aktif</span></div>
      <div class="list" id="fileList"></div>
    </div>
    <div class="dsec">
      <div class="dsec-head"><span>Riwayat</span></div>
      <div class="list" id="recentList"></div>
    </div>
    <div class="dsec">
      <div class="dsec-head"><span>Alat</span></div>
      <div class="toolgrid">
        <button class="btn" id="btnTemplate">Template</button>
        <button class="btn" id="btnExport">Export ZIP</button>
        <button class="btn" id="btnImport">Import</button>
        <button class="btn" id="btnShare">Bagikan</button>
        <button class="btn" id="btnPalette">Perintah</button>
        <button class="btn" id="btnSettings2">Pengaturan</button>
      </div>
    </div>
  </div>
</aside>

<div class="overlay" id="overlay" hidden>
  <div class="sheet" role="dialog" aria-modal="true" aria-labelledby="sheetTitle">
    <div class="sheet-head">
      <h2 id="sheetTitle">Judul</h2>
      <button class="ibtn sm" id="sheetClose" aria-label="Tutup"><svg class="icon sm"><use href="#icon-close"></use></svg></button>
    </div>
    <div class="sheet-body" id="sheetBody"></div>
  </div>
</div>

<div class="toast" id="toast"></div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/codemirror.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/mode/xml/xml.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/mode/javascript/javascript.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/mode/css/css.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/mode/htmlmixed/htmlmixed.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/edit/closebrackets.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/edit/closetag.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/edit/matchbrackets.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/selection/active-line.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/search/searchcursor.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/search/search.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/dialog/dialog.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/hint/show-hint.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/hint/html-hint.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/hint/css-hint.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/hint/javascript-hint.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.65.16/addon/comment/comment.min.js"></script>
<script src="https://unpkg.com/prettier@3/standalone.js"></script>
<script src="https://unpkg.com/prettier@3/plugins/babel.js"></script>
<script src="https://unpkg.com/prettier@3/plugins/estree.js"></script>
<script src="https://unpkg.com/prettier@3/plugins/postcss.js"></script>
<script src="https://unpkg.com/prettier@3/plugins/html.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/idb-keyval@6/dist/umd.js"></script>

<script type="module">
  import { expand } from "https://esm.sh/emmet@2.4.11";
  window.__emmetExpand = expand;
</script>

<script>
(function () {
  'use strict';

  /* =======================================================================
     UTIL
  ======================================================================= */
  var $ = function (s, r) { return (r || document).querySelector(s); };
  var $$ = function (s, r) { return Array.prototype.slice.call((r || document).querySelectorAll(s)); };

  var LS_WS = 'mcx.workspace.v1';
  var LS_SET = 'mcx.settings.v1';

  var MODES = { html: 'htmlmixed', css: 'css', js: 'javascript' };
  var HINTKEY = { html: 'html', css: 'css', js: 'javascript' };
  var LABEL = { html: 'HTML', css: 'CSS', js: 'JS' };
  var LANGS = ['html', 'css', 'js'];

  function defaultFileName(lang) {
    return lang === 'html' ? 'index.html' : (lang === 'css' ? 'style.css' : 'script.js');
  }
  function uid() {
    return 'p' + Date.now().toString(36) + Math.random().toString(36).slice(2, 7);
  }
  function timeAgo(ts) {
    if (!ts) return '';
    var d = Date.now() - ts;
    if (d < 60000) return 'baru saja';
    if (d < 3600000) return Math.floor(d / 60000) + ' menit lalu';
    if (d < 86400000) return Math.floor(d / 3600000) + ' jam lalu';
    return Math.floor(d / 86400000) + ' hari lalu';
  }

  /* =======================================================================
     STATE
  ======================================================================= */
  var workspace = { activeId: null, projects: [] };
  var settings = {
    fontSize: 13,
    tabSize: 4,
    wordWrap: false,
    autosave: true,
    autoClose: true,
    livePreview: true
  };
  var editors = {};
  var activeLang = 'html';
  var activeProject = null;
  var currentView = 'code';
  var suppress = false;
  var unsaved = false;
  var staleLangs = { html: false, css: false, js: false };
  var saveTimer = null;
  var previewTimer = null;
  var toastTimer = null;
  var lastOffset = 0;
  var errorCount = 0;
  var sticky = { ctrl: false, shift: false };

  /* =======================================================================
     DEFAULT CONTENT & TEMPLATES
  ======================================================================= */
  var DEFAULT_HTML = [
    '<h1>Halo</h1>',
    '<p>Edit kode di tab HTML, CSS, dan JS lalu tekan Run.</p>',
    '<button id="tombol" type="button">Klik saya</button>',
    '<p id="hasil"></p>'
  ].join('\n');

  var DEFAULT_CSS = [
    'body {',
    '  font-family: system-ui, sans-serif;',
    '  padding: 16px;',
    '  color: #e8ecf1;',
    '  background: #101317;',
    '}',
    '',
    'h1 {',
    '  font-size: 20px;',
    '  margin: 0 0 8px;',
    '}',
    '',
    'button {',
    '  padding: 10px 14px;',
    '  font-size: 15px;',
    '  border-radius: 6px;',
    '  border: 1px solid #2c3540;',
    '  background: #1a2029;',
    '  color: inherit;',
    '}',
    '',
    '#hasil {',
    '  color: #6fb0f0;',
    '}'
  ].join('\n');

  var DEFAULT_JS = [
    "var tombol = document.getElementById('tombol');",
    "var hasil = document.getElementById('hasil');",
    'var n = 0;',
    '',
    "tombol.addEventListener('click', function () {",
    '  n++;',
    "  hasil.textContent = 'Diklik ' + n + ' kali';",
    "  console.log('klik ke-' + n);",
    '});'
  ].join('\n');

  var TEMPLATES = {
    kosong: {
      label: 'Kosong',
      html: '',
      css: '',
      js: ''
    },
    landing: {
      label: 'Landing Page',
      html: [
        '<header class="nav">',
        '  <span class="brand">Nama Produk</span>',
        '  <a class="cta" href="#fitur">Lihat fitur</a>',
        '</header>',
        '<main>',
        '  <section class="hero">',
        '    <h1>Kelola tugas tim tanpa spreadsheet</h1>',
        '    <p>Ringkas, cepat, dan bisa dipakai dari HP.</p>',
        '    <button id="coba" class="cta" type="button">Coba sekarang</button>',
        '    <p id="pesan" class="pesan"></p>',
        '  </section>',
        '  <section id="fitur" class="fitur">',
        '    <div class="kartu"><h3>Papan tugas</h3><p>Atur prioritas dengan cepat.</p></div>',
        '    <div class="kartu"><h3>Pengingat</h3><p>Notifikasi tepat waktu.</p></div>',
        '    <div class="kartu"><h3>Laporan</h3><p>Rekap mingguan otomatis.</p></div>',
        '  </section>',
        '</main>'
      ].join('\n'),
      css: [
        '* { box-sizing: border-box; }',
        'body { margin: 0; font-family: system-ui, sans-serif; background: #0f1216; color: #e8ecf1; }',
        '.nav { display: flex; align-items: center; justify-content: space-between; padding: 14px 16px; border-bottom: 1px solid #232a33; }',
        '.brand { font-weight: 700; }',
        '.cta { background: #3b82f6; color: #06101f; border: 0; border-radius: 6px; padding: 10px 14px; font-size: 14px; text-decoration: none; cursor: pointer; }',
        '.hero { padding: 36px 16px 28px; }',
        '.hero h1 { font-size: 24px; line-height: 1.25; margin: 0 0 10px; }',
        '.hero p { color: #97a3b0; margin: 0 0 18px; }',
        '.pesan { color: #6fb0f0; margin-top: 14px; }',
        '.fitur { display: grid; gap: 12px; padding: 0 16px 40px; }',
        '.kartu { border: 1px solid #232a33; border-radius: 8px; padding: 14px; background: #151a20; }',
        '.kartu h3 { margin: 0 0 6px; font-size: 15px; }',
        '.kartu p { margin: 0; color: #97a3b0; font-size: 13px; }'
      ].join('\n'),
      js: [
        "var btn = document.getElementById('coba');",
        "var pesan = document.getElementById('pesan');",
        'var n = 0;',
        '',
        "btn.addEventListener('click', function () {",
        '  n++;',
        "  pesan.textContent = 'Terima kasih, klik ke-' + n;",
        '});',
        '',
        "console.log('Landing page siap');"
      ].join('\n')
    },
    kalkulator: {
      label: 'Kalkulator',
      html: [
        '<div class="kalkulator">',
        '  <output id="layar">0</output>',
        '  <div class="grid" id="grid">',
        '    <button type="button" data-n="C">C</button>',
        '    <button type="button" data-n="(">(</button>',
        '    <button type="button" data-n=")">)</button>',
        '    <button type="button" data-n="/" class="op">/</button>',
        '    <button type="button" data-n="7">7</button>',
        '    <button type="button" data-n="8">8</button>',
        '    <button type="button" data-n="9">9</button>',
        '    <button type="button" data-n="*" class="op">*</button>',
        '    <button type="button" data-n="4">4</button>',
        '    <button type="button" data-n="5">5</button>',
        '    <button type="button" data-n="6">6</button>',
        '    <button type="button" data-n="-" class="op">-</button>',
        '    <button type="button" data-n="1">1</button>',
        '    <button type="button" data-n="2">2</button>',
        '    <button type="button" data-n="3">3</button>',
        '    <button type="button" data-n="+" class="op">+</button>',
        '    <button type="button" data-n="0">0</button>',
        '    <button type="button" data-n=".">.</button>',
        '    <button type="button" data-n="del">Hapus</button>',
        '    <button type="button" data-n="=" class="sama">=</button>',
        '  </div>',
        '</div>'
      ].join('\n'),
      css: [
        'body { margin: 0; padding: 16px; font-family: system-ui, sans-serif; background: #0f1216; color: #e8ecf1; }',
        '.kalkulator { max-width: 320px; margin: 0 auto; }',
        '#layar { display: block; width: 100%; text-align: right; font-size: 28px; padding: 14px; border: 1px solid #232a33; border-radius: 8px; background: #151a20; margin-bottom: 10px; font-family: ui-monospace, monospace; overflow-wrap: anywhere; }',
        '.grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; }',
        '.grid button { padding: 16px 0; font-size: 17px; border-radius: 8px; border: 1px solid #232a33; background: #1a2029; color: inherit; cursor: pointer; }',
        '.grid button:active { background: #253040; }',
        '.grid .op { color: #6fb0f0; }',
        '.grid .sama { background: #3b82f6; color: #06101f; border-color: #3b82f6; grid-column: span 2; }'
      ].join('\n'),
      js: [
        "var layar = document.getElementById('layar');",
        "var grid = document.getElementById('grid');",
        "var ekspresi = '';",
        '',
        'function tampilkan() {',
        "  layar.textContent = ekspresi || '0';",
        '}',
        '',
        "grid.addEventListener('click', function (e) {",
        "  var btn = e.target.closest('button');",
        '  if (!btn) return;',
        '  var n = btn.dataset.n;',
        '',
        "  if (n === 'C') {",
        "    ekspresi = '';",
        "  } else if (n === 'del') {",
        '    ekspresi = ekspresi.slice(0, -1);',
        "  } else if (n === '=') {",
        '    if (!ekspresi) return;',
        '    if (!/^[0-9+\\-*/(). ]+$/.test(ekspresi)) {',
        "      layar.textContent = 'Input tidak valid';",
        "      ekspresi = '';",
        '      return;',
        '    }',
        '    try {',
        "      var hasil = Function('return (' + ekspresi + ')')();",
        '      ekspresi = String(hasil);',
        '      console.log(ekspresi);',
        '    } catch (err) {',
        "      layar.textContent = 'Error';",
        "      ekspresi = '';",
        '      return;',
        '    }',
        '  } else {',
        '    ekspresi += n;',
        '  }',
        '  tampilkan();',
        '});',
        '',
        'tampilkan();'
      ].join('\n')
    },
    todo: {
      label: 'To-Do List',
      html: [
        '<div class="app">',
        '  <h1>Daftar Tugas</h1>',
        '  <form id="form">',
        '    <input id="input" type="text" placeholder="Tugas baru" autocomplete="off">',
        '    <button type="submit">Tambah</button>',
        '  </form>',
        '  <ul id="daftar"></ul>',
        '  <p id="ringkas" class="ringkas"></p>',
        '</div>'
      ].join('\n'),
      css: [
        'body { margin: 0; padding: 18px 14px; font-family: system-ui, sans-serif; background: #0f1216; color: #e8ecf1; }',
        '.app { max-width: 460px; margin: 0 auto; }',
        'h1 { font-size: 20px; margin: 0 0 14px; }',
        'form { display: flex; gap: 8px; margin-bottom: 14px; }',
        'input { flex: 1; padding: 11px 12px; border-radius: 7px; border: 1px solid #232a33; background: #151a20; color: inherit; font-size: 15px; }',
        'form button { padding: 0 16px; border-radius: 7px; border: 0; background: #3b82f6; color: #06101f; font-weight: 700; font-size: 14px; cursor: pointer; }',
        'ul { list-style: none; margin: 0; padding: 0; display: flex; flex-direction: column; gap: 8px; }',
        'li { display: flex; align-items: center; gap: 10px; padding: 11px 12px; border: 1px solid #232a33; border-radius: 8px; background: #151a20; }',
        'li.selesai span { text-decoration: line-through; color: #6b7683; }',
        'li span { flex: 1; font-size: 14px; word-break: break-word; }',
        'li button { border: 0; background: none; color: #e5534b; font-size: 13px; cursor: pointer; }',
        'input[type=checkbox] { width: 18px; height: 18px; accent-color: #3b82f6; }',
        '.ringkas { color: #6b7683; font-size: 12px; margin-top: 14px; }'
      ].join('\n'),
      js: [
        "var form = document.getElementById('form');",
        "var input = document.getElementById('input');",
        "var daftar = document.getElementById('daftar');",
        "var ringkas = document.getElementById('ringkas');",
        'var tugas = [];',
        '',
        'function render() {',
        "  daftar.innerHTML = '';",
        '  tugas.forEach(function (t, i) {',
        "    var li = document.createElement('li');",
        "    if (t.selesai) li.className = 'selesai';",
        '',
        "    var cb = document.createElement('input');",
        "    cb.type = 'checkbox';",
        '    cb.checked = t.selesai;',
        "    cb.addEventListener('change', function () {",
        '      t.selesai = cb.checked;',
        '      render();',
        '    });',
        '',
        "    var span = document.createElement('span');",
        '    span.textContent = t.teks;',
        '',
        "    var hapus = document.createElement('button');",
        "    hapus.type = 'button';",
        "    hapus.textContent = 'Hapus';",
        "    hapus.addEventListener('click', function () {",
        '      tugas.splice(i, 1);',
        '      render();',
        '    });',
        '',
        '    li.appendChild(cb);',
        '    li.appendChild(span);',
        '    li.appendChild(hapus);',
        '    daftar.appendChild(li);',
        '  });',
        '',
        '  var selesai = tugas.filter(function (t) { return t.selesai; }).length;',
        "  ringkas.textContent = tugas.length + ' tugas, ' + selesai + ' selesai';",
        '}',
        '',
        "form.addEventListener('submit', function (e) {",
        '  e.preventDefault();',
        '  var teks = input.value.trim();',
        '  if (!teks) return;',
        '  tugas.push({ teks: teks, selesai: false });',
        "  input.value = '';",
        '  render();',
        '});',
        '',
        'render();'
      ].join('\n')
    },
    game: {
      label: 'Mini Game',
      html: [
        '<div class="game">',
        '  <h1>Tebak Angka</h1>',
        '  <p>Saya memilih angka antara 1 sampai 100.</p>',
        '  <form id="form">',
        '    <input id="tebak" type="number" min="1" max="100" inputmode="numeric" placeholder="Angka">',
        '    <button type="submit">Tebak</button>',
        '  </form>',
        '  <p id="pesan" class="pesan">Masukkan tebakanmu.</p>',
        '  <p id="riwayat" class="riwayat"></p>',
        '  <button id="ulang" class="ulang" type="button">Mulai ulang</button>',
        '</div>'
      ].join('\n'),
      css: [
        'body { margin: 0; padding: 22px 16px; font-family: system-ui, sans-serif; background: #0f1216; color: #e8ecf1; }',
        '.game { max-width: 420px; margin: 0 auto; }',
        'h1 { font-size: 20px; margin: 0 0 8px; }',
        'p { color: #97a3b0; font-size: 14px; }',
        'form { display: flex; gap: 8px; margin: 16px 0; }',
        'input { flex: 1; padding: 11px 12px; border-radius: 7px; border: 1px solid #232a33; background: #151a20; color: inherit; font-size: 16px; }',
        'form button { padding: 0 16px; border-radius: 7px; border: 0; background: #3b82f6; color: #06101f; font-weight: 700; cursor: pointer; }',
        '.pesan { font-size: 15px; color: #e8ecf1; min-height: 22px; }',
        '.riwayat { font-size: 12.5px; color: #6b7683; }',
        '.ulang { margin-top: 8px; padding: 9px 14px; border-radius: 7px; border: 1px solid #232a33; background: #1a2029; color: inherit; cursor: pointer; }'
      ].join('\n'),
      js: [
        'var rahasia = Math.floor(Math.random() * 100) + 1;',
        'var sisa = 8;',
        "var form = document.getElementById('form');",
        "var input = document.getElementById('tebak');",
        "var pesan = document.getElementById('pesan');",
        "var riwayat = document.getElementById('riwayat');",
        "var ulang = document.getElementById('ulang');",
        'var tebakan = [];',
        '',
        "form.addEventListener('submit', function (e) {",
        '  e.preventDefault();',
        '  var n = parseInt(input.value, 10);',
        '  if (!n || n < 1 || n > 100) {',
        "    pesan.textContent = 'Masukkan angka 1 sampai 100.';",
        '    return;',
        '  }',
        '  sisa--;',
        '  tebakan.push(n);',
        "  riwayat.textContent = 'Tebakan: ' + tebakan.join(', ');",
        "  input.value = '';",
        '',
        '  if (n === rahasia) {',
        "    pesan.textContent = 'Benar! Angkanya ' + rahasia + '.';",
        "  } else if (sisa <= 0) {",
        "    pesan.textContent = 'Kesempatan habis. Angkanya ' + rahasia + '.';",
        '  } else if (n < rahasia) {',
        "    pesan.textContent = 'Terlalu kecil. Sisa ' + sisa + ' kesempatan.';",
        '  } else {',
        "    pesan.textContent = 'Terlalu besar. Sisa ' + sisa + ' kesempatan.';",
        '  }',
        '});',
        '',
        "ulang.addEventListener('click', function () {",
        '  rahasia = Math.floor(Math.random() * 100) + 1;',
        '  sisa = 8;',
        '  tebakan = [];',
        "  riwayat.textContent = '';",
        "  pesan.textContent = 'Masukkan tebakanmu.';",
        '});'
      ].join('\n')
    }
  };

  /* =======================================================================
     TAG SHORTCUT (bukan Emmet)
  ======================================================================= */
  var TAG_EXPANSIONS = {
    html: '<html lang="id">\n  |\n</html>',
    head: '<head>\n  |\n</head>',
    body: '<body>\n  |\n</body>',
    div: '<div>|</div>',
    span: '<span>|</span>',
    p: '<p>|</p>',
    a: '<a href="">|</a>',
    img: '<img src="|" alt="">',
    ul: '<ul>\n  <li>|</li>\n</ul>',
    ol: '<ol>\n  <li>|</li>\n</ol>',
    li: '<li>|</li>',
    h1: '<h1>|</h1>',
    h2: '<h2>|</h2>',
    h3: '<h3>|</h3>',
    h4: '<h4>|</h4>',
    h5: '<h5>|</h5>',
    h6: '<h6>|</h6>',
    button: '<button type="button">|</button>',
    input: '<input type="text" name="" id="">|',
    form: '<form action="">\n  |\n</form>',
    label: '<label for="">|</label>',
    select: '<select name="" id="">\n  <option value="">|</option>\n</select>',
    option: '<option value="">|</option>',
    textarea: '<textarea name="" id="" rows="4">|</textarea>',
    table: '<table>\n  <tr>\n    <td>|</td>\n  </tr>\n</table>',
    tr: '<tr>\n  <td>|</td>\n</tr>',
    td: '<td>|</td>',
    th: '<th>|</th>',
    thead: '<thead>\n  |\n</thead>',
    tbody: '<tbody>\n  |\n</tbody>',
    section: '<section>\n  |\n</section>',
    header: '<header>\n  |\n</header>',
    footer: '<footer>\n  |\n</footer>',
    nav: '<nav>\n  |\n</nav>',
    main: '<main>\n  |\n</main>',
    aside: '<aside>\n  |\n</aside>',
    article: '<article>\n  |\n</article>',
    figure: '<figure>\n  |\n</figure>',
    blockquote: '<blockquote>|</blockquote>',
    strong: '<strong>|</strong>',
    em: '<em>|</em>',
    small: '<small>|</small>',
    pre: '<pre>|</pre>',
    code: '<code>|</code>',
    details: '<details>\n  <summary>|</summary>\n</details>',
    video: '<video src="" controls>|</video>',
    audio: '<audio src="" controls>|</audio>',
    canvas: '<canvas id="" width="300" height="150">|</canvas>',
    svg: '<svg viewBox="0 0 24 24">|</svg>',
    iframe: '<iframe src="|"></iframe>',
    script: '<script>\n|\n<\/script>',
    style: '<style>\n|\n</style>',
    link: '<link rel="stylesheet" href="|">',
    meta: '<meta name="" content="|">',
    br: '<br>|',
    hr: '<hr>|'
  };

  var COMMON_TAGS = Object.keys(TAG_EXPANSIONS);
  var COMMON_ATTRS = ['class', 'id', 'style', 'href', 'src', 'alt', 'title', 'type', 'name', 'value',
    'placeholder', 'disabled', 'checked', 'selected', 'required', 'readonly', 'for', 'target', 'rel',
    'width', 'height', 'role', 'lang', 'charset', 'content', 'defer', 'async', 'controls', 'autoplay',
    'loop', 'muted', 'poster', 'loading', 'action', 'method', 'maxlength', 'colspan', 'rowspan',
    'data-id', 'aria-label', 'viewBox', 'xmlns'];
  var COMMON_CSS = ['display', 'position', 'top', 'right', 'bottom', 'left', 'width', 'height',
    'max-width', 'min-height', 'margin', 'margin-top', 'margin-bottom', 'padding', 'padding-left',
    'border', 'border-radius', 'background', 'background-color', 'background-image', 'color',
    'font-family', 'font-size', 'font-weight', 'line-height', 'text-align', 'text-decoration',
    'letter-spacing', 'flex', 'flex-direction', 'justify-content', 'align-items', 'gap',
    'grid-template-columns', 'grid-template-rows', 'overflow', 'opacity', 'transition', 'transform',
    'box-shadow', 'cursor', 'z-index', 'box-sizing', 'outline', 'visibility', 'white-space',
    'word-break', 'object-fit', 'aspect-ratio'];
  var COMMON_JS = ['console.log', 'document.getElementById', 'document.querySelector',
    'document.querySelectorAll', 'document.createElement', 'addEventListener', 'setTimeout',
    'setInterval', 'JSON.stringify', 'JSON.parse', 'Array.from', 'Object.keys', 'Object.values',
    'Math.random', 'Math.floor', 'classList.add', 'classList.remove', 'classList.toggle',
    'innerHTML', 'textContent', 'forEach', 'map', 'filter', 'reduce', 'push', 'splice', 'slice',
    'join', 'split', 'length'];

  /* =======================================================================
     BOOTSTRAP YANG DISUNTIK KE DALAM IFRAME
  ======================================================================= */
  var BOOTSTRAP = [
    '(function(){',
    'function S(v,d){',
    'd=d||0;if(d>3)return "[Depth]";',
    'if(v===null)return null;',
    'var t=typeof v;',
    'if(t==="undefined")return "[undefined]";',
    'if(t==="string"||t==="boolean")return v;',
    'if(t==="number")return isFinite(v)?v:String(v);',
    'if(t==="function")return "[Function "+(v.name||"anonymous")+"]";',
    'if(t==="symbol")return v.toString();',
    'if(t==="bigint")return v.toString()+"n";',
    'try{if(v instanceof Error)return v.name+": "+v.message;}catch(e){}',
    'try{if(typeof Node!=="undefined"&&v instanceof Node)return "<"+v.nodeName.toLowerCase()+(v.id?"#"+v.id:"")+">";}catch(e){}',
    'if(Array.isArray(v)){var a=[];for(var i=0;i<v.length&&i<100;i++)a.push(S(v[i],d+1));if(v.length>100)a.push("...("+v.length+" item)");return a;}',
    'if(t==="object"){',
    'try{return JSON.parse(JSON.stringify(v));}catch(e){}',
    'var o={};var k=0;',
    'try{for(var key in v){if(Object.prototype.hasOwnProperty.call(v,key)){if(k++>30){o["..."]="...";break;}o[key]=S(v[key],d+1);}}}catch(e2){return "[Object]";}',
    'return o;',
    '}',
    'return String(v);',
    '}',
    'function P(m){try{parent.postMessage(m,"*");}catch(e){}}',
    'var M=["log","warn","error","info","debug"];',
    'for(var i=0;i<M.length;i++){(function(m){var o=console[m];console[m]=function(){var a=[];for(var j=0;j<arguments.length;j++)a.push(S(arguments[j],0));P({type:"console",level:m==="debug"?"log":m,args:a});try{o.apply(console,arguments);}catch(e){}};})(M[i]);}',
    'window.addEventListener("error",function(ev){try{P({type:"error",message:(ev.error&&ev.error.name?ev.error.name+": ":"")+ev.message,line:ev.lineno||0,col:ev.colno||0});}catch(e){}});',
    'window.addEventListener("unhandledrejection",function(ev){var r=ev.reason;var msg=(r&&r.name?r.name+": ":"")+(r&&r.message?r.message:String(r));P({type:"error",message:"Unhandled rejection: "+msg,line:0,col:0});});',
    '})();'
  ].join('');

  /* =======================================================================
     DOM REFS
  ======================================================================= */
  var workspaceEl, frame, logList, errList, shortcutBar, overlay, sheetTitle, sheetBody, toastEl;

  /* =======================================================================
     STORAGE
  ======================================================================= */
  function loadSettings() {
    try {
      var raw = localStorage.getItem(LS_SET);
      if (raw) {
        var d = JSON.parse(raw);
        if (d && typeof d === 'object') {
          Object.keys(settings).forEach(function (k) {
            if (typeof d[k] === typeof settings[k]) settings[k] = d[k];
          });
        }
      }
    } catch (e) { /* abaikan */ }
  }

  function persistSettings() {
    try { localStorage.setItem(LS_SET, JSON.stringify(settings)); } catch (e) { /* abaikan */ }
  }

  function makeProject(name, html, css, js) {
    return {
      id: uid(),
      name: name,
      updated: Date.now(),
      files: {
        html: { name: 'index.html', content: html || '' },
        css: { name: 'style.css', content: css || '' },
        js: { name: 'script.js', content: js || '' }
      }
    };
  }

  function normalizeProject(p) {
    if (!p.files) p.files = {};
    LANGS.forEach(function (l) {
      if (!p.files[l] || typeof p.files[l] !== 'object') {
        p.files[l] = { name: defaultFileName(l), content: '' };
      }
      if (typeof p.files[l].content !== 'string') p.files[l].content = '';
      if (!p.files[l].name || typeof p.files[l].name !== 'string') {
        p.files[l].name = defaultFileName(l);
      }
    });
    if (!p.name || typeof p.name !== 'string') p.name = 'Proyek';
    if (!p.updated) p.updated = Date.now();
    if (!p.id) p.id = uid();
    return p;
  }

  function loadWorkspace() {
    var data = null;
    try { data = JSON.parse(localStorage.getItem(LS_WS) || 'null'); } catch (e) { data = null; }
    if (data && Array.isArray(data.projects) && data.projects.length) {
      workspace = data;
      workspace.projects.forEach(normalizeProject);
      if (!workspace.projects.some(function (p) { return p.id === workspace.activeId; })) {
        workspace.activeId = workspace.projects[0].id;
      }
    } else {
      var p = makeProject('Proyek 1', DEFAULT_HTML, DEFAULT_CSS, DEFAULT_JS);
      workspace = { activeId: p.id, projects: [p] };
    }
  }

  function persistWorkspace() {
    try {
      localStorage.setItem(LS_WS, JSON.stringify({
        activeId: workspace.activeId,
        projects: workspace.projects
      }));
    } catch (e) { /* kuota penuh: abaikan */ }
  }

  /* idb-keyval untuk riwayat */
  var idb = window.idbKeyval || null;
  function idbSet(k, v) {
    if (!idb) return Promise.resolve();
    return idb.set(k, v).catch(function () { });
  }
  function idbGet(k) {
    if (!idb) return Promise.resolve(undefined);
    return idb.get(k).catch(function () { return undefined; });
  }

  function pushRecent(p) {
    idbSet('proj:' + p.id, { id: p.id, name: p.name, files: p.files });
    return idbGet('recents').then(function (recents) {
      recents = Array.isArray(recents) ? recents : [];
      recents = recents.filter(function (r) { return r.id !== p.id; });
      recents.unshift({ id: p.id, name: p.name, ts: Date.now() });
      recents = recents.slice(0, 12);
      return idbSet('recents', recents).then(function () { renderRecents(recents); });
    }).catch(function () { });
  }

  /* =======================================================================
     TOAST & SHEET
  ======================================================================= */
  function toast(msg) {
    toastEl.textContent = msg;
    toastEl.classList.add('show');
    clearTimeout(toastTimer);
    toastTimer = setTimeout(function () { toastEl.classList.remove('show'); }, 2400);
  }

  var sheetOnClose = null;

  function openSheet(title, build, onClose) {
    sheetTitle.textContent = title;
    sheetBody.innerHTML = '';
    sheetOnClose = onClose || null;
    build(sheetBody);
    overlay.hidden = false;
  }

  function closeSheet() {
    if (overlay.hidden) return;
    overlay.hidden = true;
    sheetBody.innerHTML = '';
    if (typeof sheetOnClose === 'function') {
      var f = sheetOnClose;
      sheetOnClose = null;
      f();
    }
  }

  function confirmSheet(title, msg, okLabel, onOk) {
    openSheet(title, function (body) {
      var p = document.createElement('p');
      p.className = 'sheet-msg';
      p.textContent = msg;
      body.appendChild(p);

      var actions = document.createElement('div');
      actions.className = 'sheet-actions';

      var no = document.createElement('button');
      no.className = 'btn';
      no.type = 'button';
      no.textContent = 'Batal';
      no.addEventListener('click', closeSheet);

      var yes = document.createElement('button');
      yes.className = 'btn danger';
      yes.type = 'button';
      yes.textContent = okLabel || 'Lanjutkan';
      yes.addEventListener('click', function () {
        closeSheet();
        onOk();
      });

      actions.appendChild(no);
      actions.appendChild(yes);
      body.appendChild(actions);
    });
  }

  function promptSheet(title, label, value, okLabel, onOk) {
    openSheet(title, function (body) {
      var field = document.createElement('div');
      field.className = 'field';
      var lb = document.createElement('label');
      lb.textContent = label;
      var inp = document.createElement('input');
      inp.type = 'text';
      inp.value = value || '';
      inp.spellcheck = false;
      field.appendChild(lb);
      field.appendChild(inp);
      body.appendChild(field);

      var actions = document.createElement('div');
      actions.className = 'sheet-actions';
      var no = document.createElement('button');
      no.className = 'btn';
      no.type = 'button';
      no.textContent = 'Batal';
      no.addEventListener('click', closeSheet);
      var yes = document.createElement('button');
      yes.className = 'btn primary';
      yes.type = 'button';
      yes.textContent = okLabel || 'Simpan';
      yes.addEventListener('click', function () {
        var v = inp.value.trim();
        if (!v) { inp.focus(); return; }
        closeSheet();
        onOk(v);
      });
      actions.appendChild(no);
      actions.appendChild(yes);
      body.appendChild(actions);

      setTimeout(function () { inp.focus(); inp.select(); }, 60);
    });
  }

  /* =======================================================================
     CODEMIRROR
  ======================================================================= */
  function makeHint(lang) {
    return function (cm, options) {
      var cur = cm.getCursor();
      var line = cm.getLine(cur.line);
      var before = line.slice(0, cur.ch);
      var m = before.match(/[A-Za-z0-9_.#\-]*$/);
      var word = m ? m[0] : '';
      var from = CodeMirror.Pos(cur.line, cur.ch - word.length);
      var to = cur;

      var mine;
      if (lang === 'html') {
        var lt = before.lastIndexOf('<');
        var gt = before.lastIndexOf('>');
        mine = (lt > gt) ? COMMON_ATTRS : COMMON_TAGS;
      } else if (lang === 'css') {
        mine = COMMON_CSS;
      } else {
        mine = COMMON_JS;
      }

      var result = { list: mine.slice(), from: from, to: to };

      try {
        var key = HINTKEY[lang];
        if (CodeMirror.hint && typeof CodeMirror.hint[key] === 'function') {
          var b = CodeMirror.hint[key](cm, options);
          if (b && b.list && b.list.length) {
            var merged = b.list.slice();
            for (var i = 0; i < mine.length; i++) {
              if (merged.indexOf(mine[i]) === -1) merged.push(mine[i]);
            }
            result = { list: merged, from: b.from || from, to: b.to || to };
          }
        }
      } catch (e) { /* biarkan daftar kita yang dipakai */ }

      return result;
    };
  }

  function cmExtraKeys(lang) {
    var keys = {
      'Ctrl-Space': function (cm) { cm.showHint({ hint: makeHint(lang), completeSingle: false }); },
      'Ctrl-/': 'toggleComment',
      'Cmd-/': 'toggleComment',
      'Shift-Tab': 'indentLess',
      'Alt-Up': function (cm) { moveLine(cm, -1); },
      'Alt-Down': function (cm) { moveLine(cm, 1); },
      'Shift-Ctrl-K': function (cm) { deleteLine(cm); },
      'Shift-Ctrl-D': function (cm) { duplicateLine(cm); },
      'Ctrl-S': function () { saveNow(); toast('Tersimpan'); },
      'Cmd-S': function () { saveNow(); toast('Tersimpan'); }
    };
    return keys;
  }

  function initEditors() {
    LANGS.forEach(function (lang) {
      var pane = $('.epane[data-lang="' + lang + '"]');
      pane.classList.add('active');
      var ta = $('#ta-' + lang);
      var cm = CodeMirror.fromTextArea(ta, {
        mode: MODES[lang],
        lineNumbers: true,
        theme: 'material-darker',
        lineWrapping: settings.wordWrap,
        tabSize: settings.tabSize,
        indentUnit: settings.tabSize,
        indentWithTabs: false,
        smartIndent: true,
        autoCloseBrackets: settings.autoClose,
        autoCloseTags: settings.autoClose,
        matchBrackets: true,
        styleActiveLine: true,
        showCursorWhenSelecting: true,
        viewportMargin: 12,
        extraKeys: cmExtraKeys(lang)
      });
      editors[lang] = cm;
      pane.classList.remove('active');

      cm.on('change', function (c, change) { onEditorChange(lang, change); });
      cm.on('cursorActivity', function () {
        if (lang === activeLang) updateStatus();
      });
      cm.on('focus', function () { setTimeout(refreshShortcutBar, 0); });
      cm.on('blur', function () { setTimeout(refreshShortcutBar, 160); });

      cm.on('inputRead', function (c, change) {
        if (change.origin !== '+input') return;
        var txt = change.text.join('');
        if (!/[A-Za-z<\-#.]/.test(txt)) return;
        clearTimeout(c._hintTimer);
        c._hintTimer = setTimeout(function () {
          if (c.state.completionActive) return;
          try { c.showHint({ hint: makeHint(lang), completeSingle: false }); } catch (e) { /* abaikan */ }
        }, 140);
      });
    });
    $('.epane[data-lang="html"]').classList.add('active');
  }

  function applySettings() {
    document.documentElement.style.setProperty('--code-fs', settings.fontSize + 'px');
    LANGS.forEach(function (lang) {
      var cm = editors[lang];
      if (!cm) return;
      cm.setOption('tabSize', settings.tabSize);
      cm.setOption('indentUnit', settings.tabSize);
      cm.setOption('lineWrapping', settings.wordWrap);
      cm.setOption('autoCloseBrackets', settings.autoClose);
      cm.setOption('autoCloseTags', settings.autoClose);
      cm.refresh();
    });
    updateStatus();
  }

  /* =======================================================================
     PROJECT / FILE
  ======================================================================= */
  function openProject(p, silent) {
    if (!p) return;
    if (activeProject && activeProject !== p) saveNow();
    normalizeProject(p);
    suppress = true;
    activeProject = p;
    workspace.activeId = p.id;
    editors.html.setValue(p.files.html.content);
    editors.css.setValue(p.files.css.content);
    editors.js.setValue(p.files.js.content);
    LANGS.forEach(function (l) { editors[l].clearHistory(); });
    suppress = false;

    staleLangs = { html: false, css: false, js: false };
    unsaved = false;
    updateTabDots();
    setSaveIndicator('');

    $('#btnProjectName').textContent = p.name;
    document.title = p.name + ' — Editor HTML/CSS/JS';

    if (!silent) {
      persistWorkspace();
      renderFiles();
      pushRecent(p);
      setView('code');
    } else {
      pushRecent(p);
    }
    updateStatus();
  }

  function openProjectById(id) {
    var p = workspace.projects.filter(function (x) { return x.id === id; })[0];
    if (p) { openProject(p); return; }
    idbGet('proj:' + id).then(function (snapshot) {
      if (snapshot && snapshot.files) {
        var np = normalizeProject(snapshot);
        workspace.projects.push(np);
        persistWorkspace();
        openProject(np);
        toast('Proyek dipulihkan dari riwayat');
      } else {
        toast('Proyek tidak ditemukan');
      }
    });
  }

  function newProject(name, tpl) {
    var t = TEMPLATES[tpl || 'kosong'] || TEMPLATES.kosong;
    var p = makeProject(name || 'Proyek baru', t.html, t.css, t.js);
    workspace.projects.push(p);
    persistWorkspace();
    openProject(p);
    return p;
  }

  function deleteProject(p) {
    var idx = workspace.projects.indexOf(p);
    if (idx === -1) return;
    workspace.projects.splice(idx, 1);
    if (!workspace.projects.length) {
      var np = makeProject('Proyek 1', DEFAULT_HTML, DEFAULT_CSS, DEFAULT_JS);
      workspace.projects.push(np);
      workspace.activeId = np.id;
      persistWorkspace();
      openProject(np);
    } else if (workspace.activeId === p.id) {
      persistWorkspace();
      openProject(workspace.projects[0]);
    } else {
      persistWorkspace();
      renderFiles();
    }
    idbSet('proj:' + p.id, undefined);
    toast('Proyek dihapus');
  }

  /* =======================================================================
     SAVE
  ======================================================================= */
  function setSaveIndicator(text, cls) {
    var el = $('#saveInd');
    el.textContent = text || '';
    el.className = 'save-ind' + (cls ? ' ' + cls : '');
  }

  function syncEditorsToProject() {
    if (!activeProject) return;
    activeProject.files.html.content = editors.html.getValue();
    activeProject.files.css.content = editors.css.getValue();
    activeProject.files.js.content = editors.js.getValue();
    activeProject.updated = Date.now();
  }

  function saveNow() {
    if (!activeProject) return;
    syncEditorsToProject();
    activeProject.name = $('#btnProjectName').textContent || activeProject.name;
    persistWorkspace();
    unsaved = false;
    setSaveIndicator('Tersimpan');
    idbSet('proj:' + activeProject.id, {
      id: activeProject.id,
      name: activeProject.name,
      files: activeProject.files
    });
    clearTimeout(saveTimer);
    saveTimer = null;
  }

  function scheduleSave() {
    if (!settings.autosave) {
      setSaveIndicator('Belum disimpan', 'unsaved');
      return;
    }
    setSaveIndicator('Menyimpan…');
    clearTimeout(saveTimer);
    saveTimer = setTimeout(function () {
      saveNow();
      renderFiles();
    }, 800);
  }

  /* =======================================================================
     CHANGE HANDLING
  ======================================================================= */
  function onEditorChange(lang, change) {
    if (suppress) return;
    if (change && change.origin !== 'setValue') {
      markChanged(lang);
    }
    updateStatus();
  }

  function markChanged(lang) {
    unsaved = true;
    if (lang) staleLangs[lang] = true;
    else staleLangs = { html: true, css: true, js: true };
    updateTabDots();
    if (settings.autosave) scheduleSave();
    else setSaveIndicator('Belum disimpan', 'unsaved');
    if (settings.livePreview && currentView === 'preview') schedulePreview();
    else if (settings.livePreview) schedulePreview();
  }

  function updateTabDots() {
    $$('#editorTabs button').forEach(function (b) {
      b.classList.toggle('stale', !!staleLangs[b.dataset.lang]);
    });
  }

  function schedulePreview() {
    clearTimeout(previewTimer);
    previewTimer = setTimeout(function () { runCode(false); }, 400);
  }

  function updateStatus() {
    var cm = editors[activeLang];
    if (!cm) return;
    var c = cm.getCursor();
    var sel = cm.getSelection();
    var txt = 'Baris ' + (c.line + 1) + ', Kol ' + (c.ch + 1);
    if (sel) txt += ' · ' + sel.length + ' dipilih';
    $('#stPos').textContent = txt;
    $('#stCount').textContent = cm.lineCount() + ' baris · ' + cm.getValue().length + ' karakter';
    $('#stLang').textContent = LABEL[activeLang];
  }

  /* =======================================================================
     BUILD DOC + RUN
  ======================================================================= */
  function buildDoc(html, css, js) {
    var parts = [];
    parts.push('<!DOCTYPE html>');
    parts.push('<html lang="id">');
    parts.push('<head>');
    parts.push('<meta charset="utf-8">');
    parts.push('<meta name="viewport" content="width=device-width, initial-scale=1">');
    parts.push('<style>');
    parts.push.apply(parts, String(css).split('\n'));
    parts.push('</style>');
    parts.push('</head>');
    parts.push('<body>');
    parts.push.apply(parts, String(html).split('\n'));
    parts.push('<script>' + BOOTSTRAP + '<\/script>');
    parts.push('<script>');
    var offset = parts.length;
    parts.push.apply(parts, String(js).split('\n'));
    parts.push('<\/script>');
    parts.push('</body>');
    parts.push('</html>');
    return { doc: parts.join('\n'), offset: offset };
  }

  function clearConsole() {
    logList.innerHTML = '';
    errList.innerHTML = '';
    errorCount = 0;
    updateErrBadge();
    renderConsoleEmpty();
  }

  function renderConsoleEmpty() {
    if (!logList.children.length) {
      var d = document.createElement('div');
      d.className = 'clog-empty';
      d.textContent = 'Belum ada output. Tekan Run untuk menjalankan kode.';
      logList.appendChild(d);
    }
    if (!errList.children.length) {
      var e = document.createElement('div');
      e.className = 'clog-empty';
      e.textContent = 'Tidak ada error.';
      errList.appendChild(e);
    }
  }

  function runCode(isManual) {
    clearConsole();
    var built = buildDoc(
      editors.html.getValue(),
      editors.css.getValue(),
      editors.js.getValue()
    );
    lastOffset = built.offset;
    frame.srcdoc = built.doc;
    staleLangs = { html: false, css: false, js: false };
    updateTabDots();
  }

  function updateErrBadge() {
    var show = errorCount > 0;
    var t = String(errorCount);
    [$('#errBadge'), $('#errBadge2')].forEach(function (b) {
      if (!b) return;
      b.textContent = t;
      b.classList.toggle('show', show);
    });
  }

  /* =======================================================================
     PESAN DARI IFRAME
  ======================================================================= */
  function fmtArg(v) {
    if (typeof v === 'string') return v;
    if (v === null) return 'null';
    try { return JSON.stringify(v); } catch (e) { return String(v); }
  }

  function addLog(level, args) {
    var empty = logList.querySelector('.clog-empty');
    if (empty) empty.remove();

    var el = document.createElement('div');
    el.className = 'log log-' + (level || 'log');
    var txt = document.createElement('div');
    txt.className = 'log-text';
    txt.textContent = (args || []).map(fmtArg).join(' ');
    el.appendChild(txt);
    logList.appendChild(el);

    while (logList.children.length > 400) logList.removeChild(logList.firstChild);
    logList.scrollTop = logList.scrollHeight;
  }

  function addError(d) {
    var empty = errList.querySelector('.clog-empty');
    if (empty) empty.remove();

    errorCount++;
    updateErrBadge();

    var el = document.createElement('div');
    el.className = 'log log-error';

    var txt = document.createElement('div');
    txt.className = 'log-text';
    txt.textContent = d.message || 'Error';
    el.appendChild(txt);

    var userLine = (d.line && lastOffset) ? (d.line - lastOffset) : 0;
    if (userLine > 0) {
      var jump = document.createElement('button');
      jump.type = 'button';
      jump.className = 'log-jump';
      jump.textContent = 'Lompat ke baris ' + userLine;
      jump.addEventListener('click', function () { jumpToLine(userLine); });
      el.appendChild(jump);
    }

    errList.appendChild(el);
    while (errList.children.length > 200) errList.removeChild(errList.firstChild);
    errList.scrollTop = errList.scrollHeight;
  }

  function jumpToLine(line) {
    setView('code');
    setActiveLang('js');
    var cm = editors.js;
    var l = Math.max(0, Math.min(cm.lineCount() - 1, line - 1));
    cm.setCursor({ line: l, ch: 0 });
    cm.scrollIntoView({ line: l, ch: 0 }, 140);
    cm.focus();
  }

  function onWindowMessage(e) {
    if (!frame || e.source !== frame.contentWindow) return;
    var d = e.data;
    if (!d || typeof d !== 'object') return;
    if (d.type === 'console') {
      addLog(d.level, d.args);
    } else if (d.type === 'error') {
      addError(d);
    }
  }

  /* =======================================================================
     VIEW & TAB
  ======================================================================= */
  function setView(v) {
    currentView = v;
    workspaceEl.dataset.view = v;
    $$('#viewNav button').forEach(function (b) {
      b.classList.toggle('active', b.dataset.view === v);
    });
    if (v === 'preview') {
      if (staleLangs.html || staleLangs.css || staleLangs.js) runCode(false);
    }
    if (v === 'code') {
      requestAnimationFrame(function () {
        if (editors[activeLang]) editors[activeLang].refresh();
      });
    }
    refreshShortcutBar();
  }

  function setActiveLang(lang, noFocus) {
    activeLang = lang;
    $$('#editorTabs button').forEach(function (b) {
      b.classList.toggle('active', b.dataset.lang === lang);
    });
    $$('.epane').forEach(function (p) {
      p.classList.toggle('active', p.dataset.lang === lang);
    });
    requestAnimationFrame(function () {
      var cm = editors[lang];
      if (!cm) return;
      cm.refresh();
      if (!noFocus) cm.focus();
    });
    updateStatus();
  }

  /* =======================================================================
     EDITING HELPERS
  ======================================================================= */
  function activeEditor() { return editors[activeLang]; }

  function offsetToPos(start, text, offset) {
    var before = text.slice(0, offset);
    var lines = before.split('\n');
    if (lines.length === 1) return { line: start.line, ch: start.ch + lines[0].length };
    return { line: start.line + lines.length - 1, ch: lines[lines.length - 1].length };
  }

  function insertText(text) {
    var cm = activeEditor();
    cm.replaceSelection(text);
    cm.focus();
  }

  function wrapInsert(open, close) {
    var cm = activeEditor();
    var sel = cm.getSelection();
    var start = cm.getCursor('from');
    if (sel) {
      cm.replaceSelection(open + sel + close);
      var total = open + sel + close;
      var lines = total.split('\n');
      var endLine = start.line + lines.length - 1;
      var endCh = lines.length === 1 ? start.ch + lines[0].length : lines[lines.length - 1].length;
      cm.setSelection(start, { line: endLine, ch: Math.max(0, endCh - close.length) });
    } else {
      cm.replaceSelection(open + close);
      cm.setCursor({ line: start.line, ch: start.ch + open.length });
    }
    cm.focus();
  }

  function doTab() {
    var cm = activeEditor();
    if (cm.somethingSelected()) {
      cm.indentSelection('add');
    } else {
      var c = cm.getCursor();
      var spaces = settings.tabSize - (c.ch % settings.tabSize);
      cm.replaceSelection(new Array(spaces + 1).join(' '));
    }
    cm.focus();
  }

  function wordLeft(cm, pos) {
    if (pos.ch === 0) {
      if (pos.line === 0) return pos;
      return { line: pos.line - 1, ch: cm.getLine(pos.line - 1).length };
    }
    var line = cm.getLine(pos.line);
    var i = pos.ch;
    while (i > 0 && /\s/.test(line.charAt(i - 1))) i--;
    while (i > 0 && /[\w$]/.test(line.charAt(i - 1))) i--;
    return { line: pos.line, ch: i };
  }

  function wordRight(cm, pos) {
    var line = cm.getLine(pos.line);
    if (pos.ch >= line.length) {
      if (pos.line >= cm.lastLine()) return pos;
      return { line: pos.line + 1, ch: 0 };
    }
    var i = pos.ch;
    while (i < line.length && /[\w$]/.test(line.charAt(i))) i++;
    while (i < line.length && /\s/.test(line.charAt(i))) i++;
    return { line: pos.line, ch: i };
  }

  function doArrow(dir) {
    var cm = activeEditor();
    var extend = sticky.shift;
    var ctrl = sticky.ctrl;
    var head = cm.getCursor('head');
    var target;

    if (dir === 'left') {
      target = ctrl ? wordLeft(cm, head) : { line: head.line, ch: Math.max(0, head.ch - 1) };
      if (!ctrl && head.ch === 0 && head.line > 0) {
        target = { line: head.line - 1, ch: cm.getLine(head.line - 1).length };
      }
    } else if (dir === 'right') {
      var lineText = cm.getLine(head.line);
      target = ctrl ? wordRight(cm, head) : { line: head.line, ch: head.ch + 1 };
      if (!ctrl && head.ch >= lineText.length && head.line < cm.lastLine()) {
        target = { line: head.line + 1, ch: 0 };
      }
    } else if (dir === 'up') {
      if (ctrl) target = { line: 0, ch: 0 };
      else target = { line: Math.max(0, head.line - 1), ch: head.ch };
    } else {
      if (ctrl) target = { line: cm.lastLine(), ch: cm.getLine(cm.lastLine()).length };
      else target = { line: Math.min(cm.lastLine(), head.line + 1), ch: head.ch };
    }

    if (extend) cm.extendSelection(target);
    else cm.setCursor(target);
    cm.focus();
  }

  function duplicateLine(cm) {
    var c = cm.getCursor();
    var line = cm.getLine(c.line);
    cm.replaceRange('\n' + line, { line: c.line, ch: line.length });
    cm.setCursor({ line: c.line + 1, ch: c.ch });
  }

  function deleteLine(cm) {
    var c = cm.getCursor();
    var last = cm.lastLine();
    if (last === 0) { cm.setValue(''); return; }
    if (c.line === last) {
      cm.replaceRange('',
        { line: c.line - 1, ch: cm.getLine(c.line - 1).length },
        { line: c.line, ch: cm.getLine(c.line).length });
      cm.setCursor({ line: c.line - 1, ch: 0 });
    } else {
      cm.replaceRange('', { line: c.line, ch: 0 }, { line: c.line + 1, ch: 0 });
      cm.setCursor({ line: c.line, ch: 0 });
    }
  }

  function moveLine(cm, dir) {
    var c = cm.getCursor();
    var line = c.line;
    if (dir === -1 && line === 0) return;
    if (dir === 1 && line === cm.lastLine()) return;
    var text = cm.getLine(line);
    var other = line + dir;
    var otherText = cm.getLine(other);
    cm.replaceRange(otherText, { line: line, ch: 0 }, { line: line, ch: text.length });
    cm.replaceRange(text, { line: other, ch: 0 }, { line: other, ch: otherText.length });
    cm.setCursor({ line: other, ch: c.ch });
  }

  function expandTagShortcut() {
    if (activeLang !== 'html') { toast('HTML Shortcut hanya untuk tab HTML'); return; }
    var cm = activeEditor();
    var cur = cm.getCursor();
    var line = cm.getLine(cur.line);
    var before = line.slice(0, cur.ch);
    var m = before.match(/([A-Za-z][A-Za-z0-9-]*)$/);
    if (!m) { toast('Letakkan kursor tepat setelah nama tag'); return; }
    var name = m[1].toLowerCase();
    var tpl = TAG_EXPANSIONS[name];
    if (!tpl) { toast('Tag "' + name + '" belum ada di daftar'); return; }
    var start = { line: cur.line, ch: cur.ch - name.length };
    var cursorRel = tpl.indexOf('|');
    var text = tpl.replace('|', '');
    cm.replaceRange(text, start, cur);
    cm.setCursor(offsetToPos(start, text, cursorRel < 0 ? text.length : cursorRel));
    cm.focus();
  }

  function getAbbreviation(cm) {
    var cur = cm.getCursor();
    var line = cm.getLine(cur.line);
    var i = cur.ch - 1;
    var depth = 0;
    var start = cur.ch;
    while (i >= 0) {
      var ch = line.charAt(i);
      if (ch === '}' || ch === ']' || ch === ')') depth++;
      else if (ch === '{' || ch === '[' || ch === '(') {
        if (depth === 0) break;
        depth--;
      } else if (depth === 0 && /\s/.test(ch)) break;
      start = i;
      i--;
    }
    return { text: line.slice(start, cur.ch), start: start, end: cur.ch, line: cur.line };
  }

  function runEmmet() {
    var cm = activeEditor();
    var expandFn = window.__emmetExpand;
    if (typeof expandFn !== 'function') { toast('Emmet masih dimuat, coba lagi'); return; }
    var abbr = getAbbreviation(cm);
    if (!abbr.text.trim()) { toast('Tidak ada singkatan Emmet di kursor'); return; }
    var syntax = activeLang === 'css' ? 'css' : 'html';
    var out;
    try {
      out = expandFn(abbr.text, { syntax: syntax, type: 'markup' });
    } catch (e) {
      out = '';
    }
    if (!out) { toast('Singkatan Emmet tidak dikenali'); return; }
    var from = { line: abbr.line, ch: abbr.start };
    var to = { line: abbr.line, ch: abbr.end };
    cm.replaceRange(out, from, to);
    cm.focus();
  }

  function formatActive() {
    var cm = activeEditor();
    var code = cm.getValue();
    if (!code.trim()) return;
    if (!window.prettier || !window.prettierPlugins) { toast('Formatter belum siap'); return; }

    var map = {
      html: { parser: 'html', plugins: ['html', 'babel', 'estree', 'postcss'] },
      css: { parser: 'css', plugins: ['postcss'] },
      js: { parser: 'babel', plugins: ['babel', 'estree'] }
    };
    var cfg = map[activeLang];
    var plugins = cfg.plugins.map(function (k) { return window.prettierPlugins[k]; })
      .filter(function (p) { return !!p; });

    window.prettier.format(code, {
      parser: cfg.parser,
      plugins: plugins,
      tabWidth: settings.tabSize,
      printWidth: 90,
      semi: true,
      singleQuote: false
    }).then(function (out) {
      out = String(out).replace(/\n+$/, '');
      var last = cm.lastLine();
      var from = { line: 0, ch: 0 };
      var to = { line: last, ch: cm.getLine(last).length };
      suppress = true;
      cm.replaceRange(out, from, to);
      suppress = false;
      markChanged(activeLang);
      toast('Kode diformat');
    }).catch(function (err) {
      var msg = (err && err.message) ? String(err.message).split('\n')[0] : 'kode tidak valid';
      toast('Format gagal: ' + msg);
    });
  }

  function copyAll() {
    var cm = activeEditor();
    var text = cm.getValue();
    copyText(text).then(function () { toast('Kode disalin'); })
      .catch(function () { toast('Gagal menyalin'); });
  }

  function copyText(text) {
    if (navigator.clipboard && navigator.clipboard.writeText) {
      return navigator.clipboard.writeText(text);
    }
    return new Promise(function (resolve, reject) {
      try {
        var ta = document.createElement('textarea');
        ta.value = text;
        ta.style.position = 'fixed';
        ta.style.opacity = '0';
        document.body.appendChild(ta);
        ta.select();
        var ok = document.execCommand('copy');
        document.body.removeChild(ta);
        ok ? resolve() : reject(new Error('copy gagal'));
      } catch (e) { reject(e); }
    });
  }

  /* =======================================================================
     SHORTCUT BAR
  ======================================================================= */
  var SHORTCUTS = [
    { label: '<>', act: 'wrap', open: '<', close: '>' },
    { label: '</>', act: 'text', text: '</>' },
    { label: '/', act: 'text', text: '/' },
    { label: '=', act: 'text', text: '=' },
    { label: '"', act: 'wrap', open: '"', close: '"' },
    { label: "'", act: 'wrap', open: "'", close: "'" },
    { label: '{ }', act: 'wrap', open: '{', close: '}' },
    { label: '( )', act: 'wrap', open: '(', close: ')' },
    { label: '[ ]', act: 'wrap', open: '[', close: ']' },
    { label: ';', act: 'text', text: ';' },
    { label: ':', act: 'text', text: ':' },
    { label: '#', act: 'text', text: '#' },
    { label: '@', act: 'text', text: '@' },
    { label: 'Tab', act: 'tab', wide: true },
    { label: 'Kiri', act: 'arrow', dir: 'left', icon: 'chevron-left' },
    { label: 'Atas', act: 'arrow', dir: 'up', icon: 'chevron-up' },
    { label: 'Bawah', act: 'arrow', dir: 'down', icon: 'chevron-down' },
    { label: 'Kanan', act: 'arrow', dir: 'right', icon: 'chevron-right' },
    { label: 'Ctrl', act: 'ctrl', wide: true, stickyKey: 'ctrl' },
    { label: 'Shift', act: 'shift', wide: true, stickyKey: 'shift' },
    { label: 'Esc', act: 'esc', wide: true }
  ];

  function buildShortcutBar() {
    shortcutBar.innerHTML = '';
    SHORTCUTS.forEach(function (s) {
      var b = document.createElement('button');
      b.type = 'button';
      b.className = 'sk' + (s.wide ? ' wide' : '');
      b.setAttribute('aria-label', s.label);
      if (s.icon) {
        b.innerHTML = '<svg class="icon"><use href="#icon-' + s.icon + '"></use></svg>';
      } else {
        b.textContent = s.label;
      }
      if (s.stickyKey) b.dataset.sticky = s.stickyKey;
      b.addEventListener('pointerdown', function (ev) {
        ev.preventDefault();
        handleShortcut(s);
      });
      b.addEventListener('click', function (ev) { ev.preventDefault(); });
      shortcutBar.appendChild(b);
    });
    updateStickyButtons();
  }

  function updateStickyButtons() {
    $$('.sk[data-sticky]').forEach(function (b) {
      var k = b.dataset.sticky;
      b.classList.toggle('on', !!sticky[k]);
    });
  }

  function handleShortcut(s) {
    var cm = editors[activeLang];
    if (!cm) return;
    switch (s.act) {
      case 'wrap': wrapInsert(s.open, s.close); break;
      case 'text': insertText(s.text); break;
      case 'tab': doTab(); break;
      case 'arrow': doArrow(s.dir); break;
      case 'ctrl': sticky.ctrl = !sticky.ctrl; updateStickyButtons(); cm.focus(); break;
      case 'shift': sticky.shift = !sticky.shift; updateStickyButtons(); cm.focus(); break;
      case 'esc':
        try { if (cm.closeHint) cm.closeHint(); } catch (e) { /* abaikan */ }
        closeSheet();
        sticky.ctrl = false; sticky.shift = false;
        updateStickyButtons();
        break;
      default: break;
    }
  }

  function refreshShortcutBar() {
    var ae = document.activeElement;
    var inCM = !!(ae && ae.closest && ae.closest('.CodeMirror'));
    var inBar = !!(ae && ae.closest && ae.closest('#shortcutBar'));
    var show = (inCM || inBar) && currentView === 'code';
    shortcutBar.classList.toggle('show', show);
    document.body.classList.toggle('kb', show);
  }

  /* =======================================================================
     KEYBOARD OFFSET (strategi berlapis)
  ======================================================================= */
  function updateKbOffset() {
    var off = 0;
    if (navigator.virtualKeyboard && navigator.virtualKeyboard.boundingRect) {
      off = navigator.virtualKeyboard.boundingRect.height || 0;
    } else if (window.visualViewport) {
      off = Math.max(0, window.innerHeight - window.visualViewport.height - window.visualViewport.offsetTop);
    }
    document.documentElement.style.setProperty('--kb-offset', off + 'px');
  }

  function initKeyboardOffset() {
    // Lapis 1: VirtualKeyboard API
    if (navigator.virtualKeyboard) {
      try { navigator.virtualKeyboard.overlaysContent = true; } catch (e) { /* abaikan */ }
      try {
        navigator.virtualKeyboard.addEventListener('geometrychange', updateKbOffset);
      } catch (e) { /* abaikan */ }
    }
    // Lapis 2: visualViewport
    if (window.visualViewport) {
      window.visualViewport.addEventListener('resize', updateKbOffset);
      window.visualViewport.addEventListener('scroll', updateKbOffset);
      updateKbOffset();
    }
    // Lapis 3: tidak ada keduanya -> --kb-offset tetap 0, toolbar menempel statis
    window.addEventListener('orientationchange', function () {
      setTimeout(updateKbOffset, 240);
    });
  }

  /* =======================================================================
     DRAWER
  ======================================================================= */
  function openDrawer() {
    renderFiles();
    renderRecents();
    $('#drawer').classList.add('show');
    $('#backdrop').classList.add('show');
  }
  function closeDrawer() {
    $('#drawer').classList.remove('show');
    $('#backdrop').classList.remove('show');
  }

  function renderFiles() {
    var pl = $('#projectList');
    pl.innerHTML = '';

    workspace.projects.forEach(function (p) {
      var row = document.createElement('div');
      row.className = 'frow' + (p.id === workspace.activeId ? ' active' : '');

      var main = document.createElement('button');
      main.type = 'button';
      main.className = 'frow-main';
      var nm = document.createElement('span');
      nm.className = 'frow-name';
      nm.textContent = p.name;
      var sb = document.createElement('span');
      sb.className = 'frow-sub';
      sb.textContent = timeAgo(p.updated);
      main.appendChild(nm);
      main.appendChild(sb);
      main.addEventListener('click', function () {
        openProject(p);
        closeDrawer();
      });

      var more = document.createElement('button');
      more.type = 'button';
      more.className = 'ibtn sm';
      more.setAttribute('aria-label', 'Menu proyek');
      more.innerHTML = '<svg class="icon sm"><use href="#icon-more"></use></svg>';
      more.addEventListener('click', function (e) {
        e.stopPropagation();
        projectMenu(p);
      });

      row.appendChild(main);
      row.appendChild(more);
      pl.appendChild(row);
    });

    var fl = $('#fileList');
    fl.innerHTML = '';
    if (activeProject) {
      LANGS.forEach(function (lang) {
        var f = activeProject.files[lang];
        var row = document.createElement('div');
        row.className = 'frow';

        var tag = document.createElement('span');
        tag.className = 'ftag';
        tag.textContent = LABEL[lang];

        var inp = document.createElement('input');
        inp.className = 'fname';
        inp.type = 'text';
        inp.value = f.name;
        inp.spellcheck = false;
        inp.addEventListener('change', function () {
          f.name = inp.value.trim() || defaultFileName(lang);
          inp.value = f.name;
          markChanged();
          scheduleSave();
        });

        var clr = document.createElement('button');
        clr.type = 'button';
        clr.className = 'ibtn sm';
        clr.title = 'Kosongkan berkas';
        clr.innerHTML = '<svg class="icon sm"><use href="#icon-trash"></use></svg>';
        clr.addEventListener('click', function () {
          confirmSheet('Kosongkan berkas',
            'Seluruh isi ' + f.name + ' akan dihapus. Tindakan ini tidak bisa dibatalkan.',
            'Kosongkan',
            function () {
              suppress = true;
              editors[lang].setValue('');
              suppress = false;
              markChanged(lang);
              scheduleSave();
              renderFiles();
              toast('Berkas dikosongkan');
            });
        });

        row.appendChild(tag);
        row.appendChild(inp);
        row.appendChild(clr);
        fl.appendChild(row);
      });
    }
  }

  function projectMenu(p) {
    openSheet('Proyek: ' + p.name, function (body) {
      var wrap = document.createElement('div');
      wrap.style.display = 'flex';
      wrap.style.flexDirection = 'column';
      wrap.style.gap = '8px';

      function mk(label, fn, cls) {
        var b = document.createElement('button');
        b.type = 'button';
        b.className = 'btn block' + (cls ? ' ' + cls : '');
        b.textContent = label;
        b.addEventListener('click', function () { closeSheet(); fn(); });
        wrap.appendChild(b);
      }

      mk('Buka', function () { openProject(p); closeDrawer(); });
      mk('Ganti nama', function () {
        promptSheet('Ganti nama proyek', 'Nama proyek', p.name, 'Simpan', function (v) {
          p.name = v;
          persistWorkspace();
          if (p.id === workspace.activeId) {
            $('#btnProjectName').textContent = v;
            document.title = v + ' — Editor HTML/CSS/JS';
          }
          renderFiles();
          pushRecent(p);
        });
      });
      mk('Duplikat', function () {
        var copy = normalizeProject(JSON.parse(JSON.stringify(p)));
        copy.id = uid();
        copy.name = p.name + ' (salinan)';
        copy.updated = Date.now();
        workspace.projects.push(copy);
        persistWorkspace();
        renderFiles();
        toast('Proyek diduplikasi');
      });
      mk('Hapus', function () {
        confirmSheet('Hapus proyek',
          'Proyek "' + p.name + '" beserta seluruh berkasnya akan dihapus permanen.',
          'Hapus',
          function () { deleteProject(p); closeDrawer(); });
      }, 'danger');

      body.appendChild(wrap);
    });
  }

  function renderRecents(recents) {
    var el = $('#recentList');
    if (!el) return;
    var render = function (list) {
      el.innerHTML = '';
      if (!list || !list.length) {
        var d = document.createElement('div');
        d.className = 'frow-sub';
        d.style.padding = '6px 14px 10px';
        d.textContent = 'Belum ada riwayat.';
        el.appendChild(d);
        return;
      }
      list.forEach(function (r) {
        var row = document.createElement('div');
        row.className = 'frow';
        var b = document.createElement('button');
        b.type = 'button';
        b.className = 'frow-main';
        var nm = document.createElement('span');
        nm.className = 'frow-name';
        nm.textContent = r.name || 'Proyek';
        var sb = document.createElement('span');
        sb.className = 'frow-sub';
        sb.textContent = timeAgo(r.ts);
        b.appendChild(nm);
        b.appendChild(sb);
        b.addEventListener('click', function () {
          openProjectById(r.id);
          closeDrawer();
        });
        row.appendChild(b);
        el.appendChild(row);
      });
    };
    if (recents) { render(recents); return; }
    idbGet('recents').then(function (list) { render(list); });
  }

  /* =======================================================================
     COMMANDS
  ======================================================================= */
  function getCommands() {
    return [
      { id: 'run', label: 'Run — jalankan kode', run: function () { runCode(true); setView('preview'); } },
      { id: 'format', label: 'Format kode (Prettier)', run: formatActive },
      { id: 'search', label: 'Cari dan ganti', run: function () { activeEditor().execCommand('find'); } },
      { id: 'copy', label: 'Salin seluruh kode tab aktif', run: copyAll },
      { id: 'save', label: 'Simpan sekarang', run: function () { saveNow(); renderFiles(); toast('Tersimpan'); } },
      { id: 'comment', label: 'Komentar / batalkan komentar', run: function () { activeEditor().execCommand('toggleComment'); } },
      { id: 'dup', label: 'Duplikat baris', run: function () { duplicateLine(activeEditor()); } },
      { id: 'delline', label: 'Hapus baris', run: function () { deleteLine(activeEditor()); } },
      { id: 'up', label: 'Pindah baris ke atas', run: function () { moveLine(activeEditor(), -1); } },
      { id: 'down', label: 'Pindah baris ke bawah', run: function () { moveLine(activeEditor(), 1); } },
      { id: 'tag', label: 'HTML Shortcut — perluas tag di kursor', run: expandTagShortcut },
      { id: 'emmet', label: 'Emmet — perluas singkatan', run: runEmmet },
      { id: 'hint', label: 'Autocomplete di kursor', run: function () {
        var cm = activeEditor();
        cm.showHint({ hint: makeHint(activeLang), completeSingle: false });
      } },
      { id: 'newproj', label: 'Proyek baru', run: function () { newProject('Proyek ' + (workspace.projects.length + 1)); closeDrawer(); } },
      { id: 'rename', label: 'Ganti nama proyek', run: function () {
        if (!activeProject) return;
        promptSheet('Ganti nama proyek', 'Nama proyek', activeProject.name, 'Simpan', function (v) {
          activeProject.name = v;
          $('#btnProjectName').textContent = v;
          document.title = v + ' — Editor HTML/CSS/JS';
          persistWorkspace();
          renderFiles();
        });
      } },
      { id: 'tpl', label: 'Template — buat proyek dari template', run: openTemplateSheet },
      { id: 'export', label: 'Export ZIP', run: exportZip },
      { id: 'import', label: 'Import berkas', run: importFiles },
      { id: 'share', label: 'Bagikan kode', run: shareCode },
      { id: 'clearcon', label: 'Bersihkan console', run: function () { clearConsole(); renderConsoleEmpty(); } },
      { id: 'fs', label: 'Fullscreen preview', run: togglePreviewFullscreen },
      { id: 'settings', label: 'Pengaturan', run: openSettingsSheet }
    ];
  }

  function openPalette() {
    var cmds = getCommands();
    openSheet('Perintah', function (body) {
      var inp = document.createElement('input');
      inp.className = 'palette-input';
      inp.type = 'text';
      inp.placeholder = 'Ketik untuk memfilter perintah';
      inp.autocomplete = 'off';
      body.appendChild(inp);

      var list = document.createElement('div');
      list.className = 'cmd-list';
      body.appendChild(list);

      var filtered = cmds.slice();
      var selIdx = 0;

      function render() {
        list.innerHTML = '';
        if (!filtered.length) {
          var e = document.createElement('div');
          e.className = 'empty';
          e.textContent = 'Tidak ada perintah yang cocok.';
          list.appendChild(e);
          return;
        }
        filtered.forEach(function (c, i) {
          var b = document.createElement('button');
          b.type = 'button';
          b.className = 'cmd' + (i === selIdx ? ' sel' : '');
          var s = document.createElement('span');
          s.textContent = c.label;
          b.appendChild(s);
          b.addEventListener('click', function () {
            closeSheet();
            setTimeout(function () { c.run(); }, 40);
          });
          list.appendChild(b);
        });
      }

      function filter() {
        var q = inp.value.trim().toLowerCase();
        filtered = cmds.filter(function (c) { return !q || c.label.toLowerCase().indexOf(q) !== -1; });
        selIdx = 0;
        render();
      }

      inp.addEventListener('input', filter);
      inp.addEventListener('keydown', function (e) {
        if (e.key === 'ArrowDown') { e.preventDefault(); selIdx = Math.min(filtered.length - 1, selIdx + 1); render(); }
        else if (e.key === 'ArrowUp') { e.preventDefault(); selIdx = Math.max(0, selIdx - 1); render(); }
        else if (e.key === 'Enter') {
          e.preventDefault();
          var c = filtered[selIdx];
          if (c) { closeSheet(); setTimeout(function () { c.run(); }, 40); }
        }
      });

      render();
      setTimeout(function () { inp.focus(); }, 60);
    });
  }

  function openSettingsSheet() {
    openSheet('Pengaturan', function (body) {
      function row(label, node) {
        var d = document.createElement('div');
        d.className = 'field';
        var l = document.createElement('label');
        l.textContent = label;
        d.appendChild(l);
        d.appendChild(node);
        body.appendChild(d);
      }

      function toggle(label, key, onChange) {
        var d = document.createElement('div');
        d.className = 'switch';
        var s = document.createElement('span');
        s.textContent = label;
        var t = document.createElement('button');
        t.type = 'button';
        t.className = 'tog' + (settings[key] ? ' on' : '');
        t.setAttribute('aria-label', label);
        t.addEventListener('click', function () {
          settings[key] = !settings[key];
          t.classList.toggle('on', settings[key]);
          persistSettings();
          if (onChange) onChange();
        });
        d.appendChild(s);
        d.appendChild(t);
        body.appendChild(d);
      }

      // font size
      var fsWrap = document.createElement('div');
      fsWrap.className = 'row';
      var fsRange = document.createElement('input');
      fsRange.type = 'range';
      fsRange.min = '10';
      fsRange.max = '22';
      fsRange.step = '1';
      fsRange.value = String(settings.fontSize);
      var fsVal = document.createElement('span');
      fsVal.className = 'val';
      fsVal.textContent = settings.fontSize + 'px';
      fsRange.addEventListener('input', function () {
        settings.fontSize = parseInt(fsRange.value, 10);
        fsVal.textContent = settings.fontSize + 'px';
        document.documentElement.style.setProperty('--code-fs', settings.fontSize + 'px');
        LANGS.forEach(function (l) { if (editors[l]) editors[l].refresh(); });
        persistSettings();
      });
      fsWrap.appendChild(fsRange);
      fsWrap.appendChild(fsVal);
      row('Ukuran font kode', fsWrap);

      // tab size
      var tsWrap = document.createElement('div');
      tsWrap.className = 'seg-inline';
      [2, 4].forEach(function (n) {
        var b = document.createElement('button');
        b.type = 'button';
        b.textContent = n + ' spasi';
        b.className = settings.tabSize === n ? 'active' : '';
        b.addEventListener('click', function () {
          settings.tabSize = n;
          $$('.seg-inline button', tsWrap).forEach(function (x) { x.classList.remove('active'); });
          b.classList.add('active');
          applySettings();
          persistSettings();
        });
        tsWrap.appendChild(b);
      });
      row('Ukuran indentasi (tab)', tsWrap);

      toggle('Word wrap', 'wordWrap', function () { applySettings(); });
      toggle('Autosave ke localStorage', 'autosave', function () {
        if (settings.autosave) { saveNow(); toast('Autosave aktif'); }
        else { toast('Autosave nonaktif'); }
      });
      toggle('Auto-close bracket & tag', 'autoClose', function () { applySettings(); });
      toggle('Live preview (debounce 400 ms)', 'livePreview', function () {
        toast(settings.livePreview ? 'Live preview aktif' : 'Live preview nonaktif');
      });

      var note = document.createElement('p');
      note.className = 'sheet-msg';
      note.style.marginTop = '14px';
      note.textContent = 'Catatan: preview berjalan di iframe dengan sandbox="allow-scripts" saja. ' +
        'Kode di dalam preview tidak bisa memakai localStorage, cookie, atau fetch ke origin yang sama.';
      body.appendChild(note);
    });
  }

  function openTemplateSheet() {
    openSheet('Buat proyek dari template', function (body) {
      var wrap = document.createElement('div');
      wrap.style.display = 'flex';
      wrap.style.flexDirection = 'column';
      wrap.style.gap = '8px';
      Object.keys(TEMPLATES).forEach(function (key) {
        var t = TEMPLATES[key];
        var b = document.createElement('button');
        b.type = 'button';
        b.className = 'btn block';
        b.textContent = t.label;
        b.addEventListener('click', function () {
          closeSheet();
          newProject(t.label, key);
          closeDrawer();
          toast('Proyek dibuat dari template ' + t.label);
        });
        wrap.appendChild(b);
      });
      body.appendChild(wrap);
    });
  }

  /* =======================================================================
     EXPORT / IMPORT / SHARE
  ======================================================================= */
  function downloadBlob(blob, filename) {
    var url = URL.createObjectURL(blob);
    var a = document.createElement('a');
    a.href = url;
    a.download = filename;
    document.body.appendChild(a);
    a.click();
    setTimeout(function () {
      document.body.removeChild(a);
      URL.revokeObjectURL(url);
    }, 800);
  }

  function safeName(s) {
    return (s || 'proyek').replace(/[^\w\u00C0-\u024F\- ]+/g, '_').trim() || 'proyek';
  }

  function exportZip() {
    if (!window.JSZip) { toast('JSZip tidak tersedia'); return; }
    if (!activeProject) return;
    saveNow();
    var zip = new JSZip();
    zip.file(activeProject.files.html.name || 'index.html', editors.html.getValue());
    zip.file(activeProject.files.css.name || 'style.css', editors.css.getValue());
    zip.file(activeProject.files.js.name || 'script.js', editors.js.getValue());
    zip.generateAsync({ type: 'blob' }).then(function (blob) {
      downloadBlob(blob, safeName(activeProject.name) + '.zip');
      toast('ZIP diunduh');
    }).catch(function () { toast('Gagal membuat ZIP'); });
  }

  function importFiles() {
    var input = document.createElement('input');
    input.type = 'file';
    input.multiple = true;
    input.accept = '.html,.htm,.css,.js,.txt,.zip,application/zip';
    input.addEventListener('change', function () {
      var files = Array.prototype.slice.call(input.files || []);
      if (!files.length) return;
      var zipFile = files.filter(function (f) { return /\.zip$/i.test(f.name); })[0];
      if (zipFile && window.JSZip) {
        zipFile.arrayBuffer().then(function (buf) {
          return JSZip.loadAsync(buf);
        }).then(function (zip) {
          var names = Object.keys(zip.files);
          var picks = { html: null, css: null, js: null };
          names.forEach(function (n) {
            var base = n.split('/').pop().toLowerCase();
            if (!picks.html && /\.html?$/.test(base)) picks.html = n;
            else if (!picks.css && /\.css$/.test(base)) picks.css = n;
            else if (!picks.js && /\.js$/.test(base)) picks.js = n;
          });
          var jobs = [];
          if (picks.html) jobs.push(zip.files[picks.html].async('string').then(function (t) {
            suppress = true; editors.html.setValue(t); suppress = false;
            activeProject.files.html.name = picks.html.split('/').pop();
          }));
          if (picks.css) jobs.push(zip.files[picks.css].async('string').then(function (t) {
            suppress = true; editors.css.setValue(t); suppress = false;
            activeProject.files.css.name = picks.css.split('/').pop();
          }));
          if (picks.js) jobs.push(zip.files[picks.js].async('string').then(function (t) {
            suppress = true; editors.js.setValue(t); suppress = false;
            activeProject.files.js.name = picks.js.split('/').pop();
          }));
          return Promise.all(jobs);
        }).then(function () {
          markChanged();
          saveNow();
          renderFiles();
          toast('Import ZIP selesai');
        }).catch(function () { toast('Gagal membaca ZIP'); });
        return;
      }

      var jobs = files.map(function (f) {
        return f.text().then(function (text) {
          var n = f.name.toLowerCase();
          if (/\.html?$/.test(n)) {
            suppress = true; editors.html.setValue(text); suppress = false;
            activeProject.files.html.name = f.name;
          } else if (/\.css$/.test(n)) {
            suppress = true; editors.css.setValue(text); suppress = false;
            activeProject.files.css.name = f.name;
          } else if (/\.js$/.test(n)) {
            suppress = true; editors.js.setValue(text); suppress = false;
            activeProject.files.js.name = f.name;
          }
        });
      });
      Promise.all(jobs).then(function () {
        markChanged();
        saveNow();
        renderFiles();
        toast('Import selesai');
      }).catch(function () { toast('Gagal membaca berkas'); });
    });
    input.click();
  }

  function buildShareText() {
    var built = buildDoc(
      editors.html.getValue(),
      editors.css.getValue(),
      editors.js.getValue()
    );
    return built.doc;
  }

  function shareCode() {
    var text = buildShareText();
    var title = activeProject ? activeProject.name : 'Kode';
    if (navigator.share) {
      navigator.share({ title: title, text: text }).catch(function (err) {
        if (err && err.name === 'AbortError') return;
        copyText(text).then(function () { toast('Kode disalin ke clipboard'); })
          .catch(function () { toast('Gagal membagikan'); });
      });
    } else {
      copyText(text).then(function () { toast('Kode disalin ke clipboard'); })
        .catch(function () { toast('Gagal menyalin'); });
    }
  }

  function togglePreviewFullscreen() {
    setView('preview');
    $('#view-preview').classList.toggle('fs');
  }

  /* =======================================================================
     BIND UI
  ======================================================================= */
  function bindUI() {
    // header
    $('#btnDrawer').addEventListener('click', openDrawer);
    $('#drawerClose').addEventListener('click', closeDrawer);
    $('#backdrop').addEventListener('click', closeDrawer);
    $('#btnRun').addEventListener('click', function () {
      runCode(true);
      setView('preview');
    });
    $('#btnSettings').addEventListener('click', openSettingsSheet);
    $('#btnSettings2').addEventListener('click', function () { closeDrawer(); openSettingsSheet(); });
    $('#btnProjectName').addEventListener('click', function () {
      if (!activeProject) return;
      promptSheet('Ganti nama proyek', 'Nama proyek', activeProject.name, 'Simpan', function (v) {
        activeProject.name = v;
        $('#btnProjectName').textContent = v;
        document.title = v + ' — Editor CSS/JS';
        persistWorkspace();
        renderFiles();
        pushRecent(activeProject);
      });
    });

    // view nav
    $$('#viewNav button').forEach(function (b) {
      b.addEventListener('click', function () { setView(b.dataset.view); });
    });

    // editor tabs
    $$('#editorTabs button').forEach(function (b) {
      b.addEventListener('click', function () { setActiveLang(b.dataset.lang); });
    });

    // editor actions
    $('#btnTag').addEventListener('click', expandTagShortcut);
    $('#btnEmmet').addEventListener('click', runEmmet);
    $('#btnFormat').addEventListener('click', formatActive);
    $('#btnSearch').addEventListener('click', function () { activeEditor().execCommand('find'); });
    $('#btnComment').addEventListener('click', function () { activeEditor().execCommand('toggleComment'); });
    $('#btnCopy').addEventListener('click', copyAll);
    $('#btnLines').addEventListener('click', openLineMenu);

    // preview
    $('#btnReload').addEventListener('click', function () { runCode(true); });
    $('#btnFs').addEventListener('click', togglePreviewFullscreen);

    // console
    $('#btnClearCon').addEventListener('click', function () {
      clearConsole();
      renderConsoleEmpty();
    });
    $$('#conSeg button').forEach(function (b) {
      b.addEventListener('click', function () {
        var panel = b.dataset.panel;
        $$('#conSeg button').forEach(function (x) { x.classList.toggle('active', x === b); });
        logList.hidden = panel !== 'log';
        errList.hidden = panel !== 'err';
      });
    });

    // drawer tools
    $('#btnNewProject').addEventListener('click', function () {
      promptSheet('Proyek baru', 'Nama proyek', 'Proyek ' + (workspace.projects.length + 1), 'Buat', function (v) {
        newProject(v);
        closeDrawer();
        toast('Proyek dibuat');
      });
    });
    $('#btnTemplate').addEventListener('click', openTemplateSheet);
    $('#btnExport').addEventListener('click', function () { closeDrawer(); exportZip(); });
    $('#btnImport').addEventListener('click', function () { closeDrawer(); importFiles(); });
    $('#btnShare').addEventListener('click', function () { closeDrawer(); shareCode(); });
    $('#btnPalette').addEventListener('click', function () { closeDrawer(); openPalette(); });

    // sheet
    $('#sheetClose').addEventListener('click', closeSheet);
    overlay.addEventListener('click', function (e) {
      if (e.target === overlay) closeSheet();
    });

    // message dari iframe
    window.addEventListener('message', onWindowMessage);

    // keyboard shortcut desktop
    document.addEventListener('keydown', function (e) {
      var mod = e.ctrlKey || e.metaKey;
      if (mod && e.shiftKey && (e.key === 'P' || e.key === 'p')) {
        e.preventDefault();
        openPalette();
      } else if (mod && (e.key === 's' || e.key === 'S')) {
        e.preventDefault();
        saveNow();
        renderFiles();
        toast('Tersimpan');
      } else if (mod && e.key === 'Enter') {
        e.preventDefault();
        runCode(true);
        setView('preview');
      } else if (e.key === 'Escape') {
        if (!overlay.hidden) closeSheet();
        else closeDrawer();
      }
    });

    // beforeunload (best-effort, bukan pengaman utama)
    window.addEventListener('beforeunload', function (e) {
      if (unsaved) {
        e.preventDefault();
        e.returnValue = '';
        return '';
      }
      return undefined;
    });

    // swipe pada tab bar & preview (tidak mengganggu scroll editor)
    enableSwipe($('#viewNav'), function (dir) {
      var order = ['code', 'preview', 'console'];
      var i = order.indexOf(currentView);
      var next = order[Math.max(0, Math.min(order.length - 1, i + (dir === 'left' ? 1 : -1)))];
      if (next !== currentView) setView(next);
    });
    enableSwipe($('#editorTabs'), function (dir) {
      var i = LANGS.indexOf(activeLang);
      var next = LANGS[Math.max(0, Math.min(LANGS.length - 1, i + (dir === 'left' ? 1 : -1)))];
      if (next !== activeLang) setActiveLang(next);
    });

    window.addEventListener('resize', function () {
      if (editors[activeLang]) editors[activeLang].refresh();
    });
  }

  function openLineMenu() {
    openSheet('Operasi baris', function (body) {
      var wrap = document.createElement('div');
      wrap.style.display = 'flex';
      wrap.style.flexDirection = 'column';
      wrap.style.gap = '8px';
      var items = [
        ['Duplikat baris', function () { duplicateLine(activeEditor()); }],
        ['Hapus baris', function () { deleteLine(activeEditor()); }],
        ['Pindah baris ke atas', function () { moveLine(activeEditor(), -1); }],
        ['Pindah baris ke bawah', function () { moveLine(activeEditor(), 1); }],
        ['Komentar / batalkan komentar', function () { activeEditor().execCommand('toggleComment'); }],
        ['Indentasi tambah', function () { activeEditor().indentSelection('add'); }],
        ['Indentasi kurang', function () { activeEditor().indentSelection('subtract'); }]
      ];
      items.forEach(function (it) {
        var b = document.createElement('button');
        b.type = 'button';
        b.className = 'btn block';
        b.textContent = it[0];
        b.addEventListener('click', function () {
          closeSheet();
          setTimeout(function () { it[1](); activeEditor().focus(); }, 30);
        });
        wrap.appendChild(b);
      });
      body.appendChild(wrap);
    });
  }

  function enableSwipe(el, cb) {
    if (!el) return;
    var sx = 0, sy = 0, tracking = false;
    el.addEventListener('touchstart', function (e) {
      if (e.touches.length !== 1) { tracking = false; return; }
      sx = e.touches[0].clientX;
      sy = e.touches[0].clientY;
      tracking = true;
    }, { passive: true });
    el.addEventListener('touchend', function (e) {
      if (!tracking) return;
      tracking = false;
      var t = e.changedTouches[0];
      var dx = t.clientX - sx;
      var dy = t.clientY - sy;
      if (Math.abs(dx) > 55 && Math.abs(dy) < 45) {
        cb(dx < 0 ? 'left' : 'right');
      }
    }, { passive: true });
  }

  /* =======================================================================
     INIT
  ======================================================================= */
  function init() {
    workspaceEl = $('#workspace');
    frame = $('#preview');
    logList = $('#logList');
    errList = $('#errList');
    shortcutBar = $('#shortcutBar');
    overlay = $('#overlay');
    sheetTitle = $('#sheetTitle');
    sheetBody = $('#sheetBody');
    toastEl = $('#toast');

    loadSettings();
    loadWorkspace();

    initEditors();
    applySettings();

    var p = workspace.projects.filter(function (x) { return x.id === workspace.activeId; })[0] || workspace.projects[0];
    openProject(p, true);

    buildShortcutBar();
    bindUI();
    renderFiles();
    renderRecents();
    renderConsoleEmpty();
    updateErrBadge();
    setView('code');
    setActiveLang('html', true);
    initKeyboardOffset();
    updateKbOffset();

    if (navigator.storage && navigator.storage.persist) {
      try { navigator.storage.persist(); } catch (e) { /* abaikan */ }
    }

    // muat ulang preview pertama kali
    setTimeout(function () { runCode(false); }, 300);
  }

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', init);
  } else {
    init();
  }

})();
</script>
</body>
</html>
