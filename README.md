ROUND 1

<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Spin the Wheel</title>
<style>
:root{--bg:#1d2a44;--panel:#26365a;--ink:#fff7e6;--accent:#ffb400;box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
*{box-sizing:border-box}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
body{margin:0;background:var(--bg);color:var(--ink);font-family:"Trebuchet MS",system-ui,sans-serif;min-height:100vh}
main{max-width:1000px;margin:0 auto;padding:24px 16px 40px;display:grid;gap:28px;grid-template-columns:1fr}
main.admin{max-width:1000px}
@media(min-width:860px){main.admin{grid-template-columns:1.3fr 1fr;align-items:start}}
h1{font-size:clamp(2rem,6vw,3rem);margin:0 0 4px}
.sub{opacity:.75;margin:0 0 16px}
.stage{position:relative;width:min(100%,460px);margin:0 auto}
canvas{width:100%;height:auto;display:block}
.pointer{position:absolute;top:-6px;left:50%;transform:translateX(-50%);border-left:16px solid transparent;border-right:16px solid transparent;border-top:32px solid var(--accent);filter:drop-shadow(0 2px 2px rgba(0,0,0,.4));z-index:2}
button{font:inherit;cursor:pointer;border:0;border-radius:999px}
#spin{display:block;margin:20px auto 0;padding:14px 48px;font-size:1.3rem;font-weight:700;background:var(--accent);color:var(--bg)}
#spin:disabled{opacity:.6;cursor:default}
button:focus-visible,textarea:focus-visible,input:focus-visible,select:focus-visible{outline:3px solid #fff;outline-offset:2px}
#result{text-align:center;min-height:3.2em;margin-top:16px;font-size:1.5rem;font-weight:700}
#result small{display:block;opacity:.75;font-size:.9rem;font-weight:400}
#choices{text-align:center;margin-top:8px}
#choices p{margin:0 0 10px;opacity:.75}
#choices .list{display:flex;flex-wrap:wrap;gap:10px;justify-content:center}
#choices button{padding:10px 20px;background:var(--panel);color:var(--ink);border:2px solid var(--accent);font-weight:600}
#choices button:hover{background:var(--accent);color:var(--bg)}
#history{opacity:.8;font-size:.9rem;padding-left:20px}
#adminPanel{background:var(--panel);border-radius:16px;padding:18px;display:grid;gap:12px}
#adminPanel h2{margin:0;font-size:1.1rem}
#adminPanel label{font-size:.85rem;opacity:.85;display:grid;gap:4px}
#adminPanel input[type=text],#adminPanel textarea,#adminPanel select{width:100%;font:inherit;padding:8px;border-radius:8px;border:1px solid #ffffff44;background:#00000033;color:var(--ink)}
textarea{min-height:120px;resize:vertical}
.row{display:flex;gap:8px;flex-wrap:wrap;align-items:center}
.row button,.sm{padding:7px 14px;background:#ffffff26;color:var(--ink)}
.swatch{display:flex;gap:4px;align-items:center}
input[type=color]{width:38px;height:32px;padding:0;border:0;background:none;cursor:pointer}
.note{font-size:.8rem;opacity:.7;margin:0}
</style>
</head>
<body>
<main id="main">
<section>
<h1 id="title"></h1>
<p class="sub" id="sub"></p>
<div class="stage"><div class="pointer"></div><canvas id="wheel" width="800" height="800" aria-label="Prize wheel"></canvas></div>
<button id="spin">Spin</button>
<div id="result" aria-live="polite"></div>
<div id="choices" hidden></div>
<ol id="history" aria-label="Spin history"></ol>
</section>
<aside id="adminPanel" hidden>
<h2>Admin</h2>
<label>Round
<select id="roundSel"></select></label>
<div class="row">
<button id="dup">Duplicate round</button>
<button id="del">Delete round</button>
<button id="resetRound">Restore removed options</button>
</div>
<label>Round name (admin only)<input type="text" id="fName"></label>
<label>Heading<input type="text" id="fTitle"></label>
<label>Subheading<input type="text" id="fSub"></label>
<label>Wheel options (one per line)<textarea id="fOpts"></textarea></label>
<label>Choice list after the spin (one per line, empty to skip)<textarea id="fChoices" style="min-height:90px"></textarea></label>
<label><span><input type="checkbox" id="fRemove"> Remove winner from wheel after each spin</span></label>
<div class="row">
<span class="swatch">Background <input type="color" id="cBg"></span>
<span class="swatch">Panels <input type="color" id="cPanel"></span>
<span class="swatch">Text <input type="color" id="cInk"></span>
<span class="swatch">Accent <input type="color" id="cAccent"></span>
</div>
<div><div class="note">Wheel slice colors</div>
<div class="row" id="palette"></div>
<div class="row" style="margin-top:6px"><button id="addC" class="sm">Add color</button><button id="remC" class="sm">Remove last</button></div></div>
<label>Copy / paste box
<textarea id="code" placeholder="Wheel code appears here, or paste one and click Add as new round"></textarea></label>
<div class="row">
<button id="copyRound">Copy this round</button>
<button id="pasteRound">Add as new round</button>
<button id="copyAll">Copy all settings</button>
</div>
<label>Admin PIN<input type="text" id="fPin"></label>
<p class="note">Guests only see the active round and no editing tools. Changes you make here are saved in this browser. To put them on the shared link, click "Copy all settings" and send them to Claude to publish.</p>
</aside>
</main>
<script>
(function(){
var DEFAULT={ver:1,pin:"1234",active:0,rounds:[{
name:"Round 1",title:"Spin the wheel",sub:"Spin for the Boss/Raid and then choose the challenge.",
bg:"#1d2a44",panel:"#26365a",ink:"#fff7e6",accent:"#ffb400",
palette:["#e4572e","#ffb400","#29a19c","#5b8def","#a76de0","#f28ab2","#6cc551","#ff8a3d"],
options:["GWD","TOA","TOB","Nex","CoX","Maggot King","Muspah","Nightmare/PNM"],
choices:["100 KC","150 KC","Purple","25m Uniques","2 GM CAs"],removeWinner:false}]};
var $=function(i){return document.getElementById(i)};
var clone=function(o){return JSON.parse(JSON.stringify(o))};
var cfg=clone(DEFAULT);
try{var s=localStorage.getItem("wheelCfg");if(s){var p=JSON.parse(s);if(p&&p.ver===DEFAULT.ver&&p.rounds&&p.rounds.length)cfg=p;}}catch(e){}
function save(){try{localStorage.setItem("wheelCfg",JSON.stringify(cfg));}catch(e){}}
function R(){return cfg.rounds[cfg.active];}
var canvas=$("wheel"),ctx=canvas.getContext("2d"),spinBtn=$("spin"),result=$("result");
var opts=[],angle=0,spinning=false,pending=false,unlocked=false;

function textOn(hex){var n=parseInt(hex.slice(1),16),r=n>>16,g=(n>>8)&255,b=n&255;return (0.299*r+0.587*g+0.114*b)>150?"#1d2a44":"#ffffff";}
function theme(){var r=R(),st=document.documentElement.style;st.setProperty("--bg",r.bg);st.setProperty("--panel",r.panel);st.setProperty("--ink",r.ink);st.setProperty("--accent",r.accent);}
function draw(){
var W=canvas.width,c=W/2,r=c-10,n=opts.length,pal=R().palette;
ctx.clearRect(0,0,W,W);
if(n<2){ctx.fillStyle=R().ink;ctx.font="36px sans-serif";ctx.textAlign="center";ctx.fillText("Add at least 2 options",c,c);return;}
var seg=2*Math.PI/n;
for(var i=0;i<n;i++){
var col=pal[i%pal.length],a0=angle+i*seg-Math.PI/2,a1=a0+seg;
ctx.beginPath();ctx.moveTo(c,c);ctx.arc(c,c,r,a0,a1);ctx.closePath();
ctx.fillStyle=col;ctx.fill();ctx.strokeStyle=R().bg;ctx.lineWidth=4;ctx.stroke();
ctx.save();ctx.translate(c,c);ctx.rotate(a0+seg/2);ctx.textAlign="right";ctx.fillStyle=textOn(col);
var size=Math.max(18,Math.min(38,300/n+10));ctx.font="700 "+size+"px 'Trebuchet MS',sans-serif";
var t=opts[i];if(t.length>18)t=t.slice(0,17)+"…";
ctx.fillText(t,r-24,size/3);ctx.restore();
}
ctx.beginPath();ctx.arc(c,c,46,0,7);ctx.fillStyle=R().bg;ctx.fill();ctx.lineWidth=6;ctx.strokeStyle=R().accent;ctx.stroke();
}
function lines(v){return v.split("\n").map(function(x){return x.trim()}).filter(Boolean);}
function renderRound(){
var r=R();opts=r.options.slice(0,24);pending=false;spinning=false;spinBtn.disabled=false;
$("title").textContent=r.title;$("sub").textContent=r.sub;document.title=r.title;
result.innerHTML="";$("choices").hidden=true;$("history").innerHTML="";
theme();draw();
}
function spin(){
if(spinning||pending||opts.length<2)return;
spinning=true;spinBtn.disabled=true;result.textContent="";
var n=opts.length,seg=2*Math.PI/n,reduce=window.matchMedia("(prefers-reduced-motion: reduce)").matches;
var start=angle,total=(reduce?1:5+Math.floor(Math.random()*3))*2*Math.PI+Math.random()*2*Math.PI;
var dur=reduce?600:5000,t0=null;
function frame(t){if(!t0)t0=t;var p=Math.min((t-t0)/dur,1),e=1-Math.pow(1-p,4);angle=start+total*e;draw();if(p<1)requestAnimationFrame(frame);else finish(seg);}
requestAnimationFrame(frame);
}
function show(main,small){result.innerHTML="";var s=document.createElement("span");s.textContent=main;result.appendChild(s);var m=document.createElement("small");m.textContent=small;result.appendChild(m);}
function finish(seg){
var n=opts.length,a=((angle%(2*Math.PI))+2*Math.PI)%(2*Math.PI);
var idx=Math.floor((((2*Math.PI-a)%(2*Math.PI))+1e-9)/seg)%n,win=opts[idx];
show(win,"You landed on this one");
var li=document.createElement("li");li.textContent=win;var h=$("history");h.insertBefore(li,h.firstChild);
if(R().removeWinner&&n>2){opts.splice(idx,1);draw();}
spinning=false;spinBtn.disabled=false;
showChoices(win,li);
}
function showChoices(win,li){
var list=R().choices,box=$("choices");box.innerHTML="";
if(!list.length){box.hidden=true;return;}
pending=true;spinBtn.disabled=true;box.hidden=false;
var p=document.createElement("p");p.textContent="Now choose one:";box.appendChild(p);
var wrap=document.createElement("div");wrap.className="list";
list.forEach(function(item){
var b=document.createElement("button");b.textContent=item;
b.addEventListener("click",function(){
li.textContent=win+" → "+item;box.innerHTML="";box.hidden=true;
show(win+" → "+item,"Choice locked in");pending=false;spinBtn.disabled=false;
});
wrap.appendChild(b);
});
box.appendChild(wrap);
}
spinBtn.addEventListener("click",spin);

/* ---------- admin ---------- */
function loadForm(){
var r=R();
$("fName").value=r.name;$("fTitle").value=r.title;$("fSub").value=r.sub;
$("fOpts").value=r.options.join("\n");$("fChoices").value=r.choices.join("\n");
$("fRemove").checked=!!r.removeWinner;
$("cBg").value=r.bg;$("cPanel").value=r.panel;$("cInk").value=r.ink;$("cAccent").value=r.accent;
$("fPin").value=cfg.pin;renderPalette();
}
function renderSel(){
var sel=$("roundSel");sel.innerHTML="";
cfg.rounds.forEach(function(r,i){var o=document.createElement("option");o.value=i;o.textContent=r.name;sel.appendChild(o);});
sel.value=cfg.active;
}
function renderPalette(){
var box=$("palette");box.innerHTML="";
R().palette.forEach(function(c,i){
var inp=document.createElement("input");inp.type="color";inp.value=c;inp.setAttribute("aria-label","Slice color "+(i+1));
inp.addEventListener("input",function(){R().palette[i]=inp.value;save();draw();});
box.appendChild(inp);
});
}
function bind(id,key,after){
$(id).addEventListener("input",function(){
var r=R(),el=$(id);
if(key==="options"||key==="choices")r[key]=lines(el.value);
else if(el.type==="checkbox")r[key]=el.checked;
else r[key]=el.value;
save();
if(key==="options"){if(!spinning){opts=r.options.slice(0,24);draw();}}
else if(key==="name")renderSel();
else if(key==="title"||key==="sub"){$("title").textContent=r.title;$("sub").textContent=r.sub;document.title=r.title;}
else if(key==="removeWinner"||key==="choices"){}
else{theme();draw();}
});
}
function checkAdmin(){
var want=location.hash==="#admin";
if(want&&!unlocked){
var v=window.prompt("Admin PIN");
if(v!==null&&v===cfg.pin)unlocked=true;else{location.hash="";want=false;}
}
$("adminPanel").hidden=!(want&&unlocked);
$("main").className=(want&&unlocked)?"admin":"";
if(want&&unlocked){renderSel();loadForm();}
}
function flash(msg){var b=$("code");b.value=msg;}
function copyText(t){
$("code").value=t;$("code").select();
try{navigator.clipboard.writeText(t);}catch(e){}
}
[["fName","name"],["fTitle","title"],["fSub","sub"],["fOpts","options"],["fChoices","choices"],["fRemove","removeWinner"],
["cBg","bg"],["cPanel","panel"],["cInk","ink"],["cAccent","accent"]].forEach(function(a){bind(a[0],a[1]);});
$("fPin").addEventListener("input",function(){if($("fPin").value){cfg.pin=$("fPin").value;save();}});
$("roundSel").addEventListener("change",function(){cfg.active=+$("roundSel").value;save();renderRound();loadForm();});
$("dup").addEventListener("click",function(){var c=clone(R());c.name=c.name+" copy";cfg.rounds.push(c);cfg.active=cfg.rounds.length-1;save();renderSel();renderRound();loadForm();});
$("del").addEventListener("click",function(){
if(cfg.rounds.length<2){flash("You need at least one round.");return;}
if(!window.confirm("Delete "+R().name+"?"))return;
cfg.rounds.splice(cfg.active,1);cfg.active=0;save();renderSel();renderRound();loadForm();
});
$("resetRound").addEventListener("click",function(){renderRound();});
$("addC").addEventListener("click",function(){R().palette.push("#888888");save();renderPalette();draw();});
$("remC").addEventListener("click",function(){if(R().palette.length>2){R().palette.pop();save();renderPalette();draw();}});
$("copyRound").addEventListener("click",function(){copyText(JSON.stringify(R()));});
$("copyAll").addEventListener("click",function(){copyText(JSON.stringify(cfg));});
$("pasteRound").addEventListener("click",function(){
try{
var o=JSON.parse($("code").value);
if(o.rounds){cfg=o;cfg.ver=DEFAULT.ver;cfg.active=0;}
else{if(!o.options||!o.palette)throw 0;cfg.rounds.push(o);cfg.active=cfg.rounds.length-1;}
save();renderSel();renderRound();loadForm();$("code").value="";
}catch(e){flash("That doesn't look like wheel code. Use Copy this round or Copy all settings.");}
});
window.addEventListener("hashchange",checkAdmin);
renderRound();checkAdmin();
})();
</script>
</body>
</html>
