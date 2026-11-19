<!DOCTYPE html>
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  base: "/Puzzle-Game-Generator/",
  plugins: [react()],
});
npm install
npm run build
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Monkey League</title>
<style>
:root{--bg:#10201a;--ink:#fff7d6;--blue:#2f8cff;--orange:#ff8a1f;--banana:#ffd93b;box-sizing:border-box}
@media (prefers-color-scheme: dark){:root{--bg:#0a1511}}
:root[data-theme="dark"]{--bg:#0a1511}
html,body{height:100%;margin:0;background:var(--bg);overflow:hidden;font-family:"Trebuchet MS","Segoe UI",sans-serif;color:var(--ink)}
canvas{display:block;width:100%;height:100%}
#hud{position:fixed;inset:0;pointer-events:none;padding-top:env(safe-area-inset-top,0px)}
#score{position:absolute;top:10px;left:50%;transform:translateX(-50%);display:flex;gap:0;align-items:stretch;font-weight:800;text-shadow:0 2px 6px #0008}
#score div{padding:6px 18px;font-size:30px;min-width:44px;text-align:center}
#sb{background:var(--blue)}#so{background:var(--orange)}#st{background:#000a;font-size:24px!important;min-width:80px!important}
#msg{position:absolute;top:36%;width:100%;text-align:center;font-size:72px;font-weight:900;color:var(--banana);text-shadow:0 4px 0 #0008}
#boost{position:absolute;right:22px;bottom:calc(22px + env(safe-area-inset-bottom,0px));width:120px;height:120px;border-radius:50%;background:conic-gradient(var(--banana) calc(var(--b,1)*360deg),#0007 0);display:flex;align-items:center;justify-content:center}
#boost b{width:88px;height:88px;border-radius:50%;background:#000c;display:flex;align-items:center;justify-content:center;font-size:34px}
#menu{position:fixed;inset:0;pointer-events:none;font-family:"Arial Black","Segoe UI",sans-serif;font-style:italic;color:#fff}
#menu>*{pointer-events:auto}
.hid{display:none!important}
.top{position:absolute;top:0;left:0;right:0;padding:calc(10px + env(safe-area-inset-top,0px)) 24px 14px;display:flex;align-items:center;gap:26px;background:linear-gradient(#0008,#0000);pointer-events:none!important}
.logo{font-size:26px;font-weight:900;color:var(--banana);text-shadow:0 3px 0 #6b4a00;transform:skewX(-8deg)}
.logo span{color:#fff;margin-left:6px}
.tabs{display:flex;gap:4px;pointer-events:auto}
.tab{background:none;border:0;border-bottom:4px solid transparent;color:#fffa;font:inherit;font-size:18px;padding:8px 18px;cursor:pointer}
.tab.on{color:#fff;border-color:var(--banana)}
#modes,#pLock{position:absolute;left:24px;top:92px;width:min(340px,44vw);display:flex;flex-direction:column;gap:12px}
#pLock{background:#0b1030cc;border-radius:8px;padding:14px 16px;gap:14px}
.mode{display:block;text-align:left;border:0;cursor:pointer;color:#fff;font:inherit;padding:12px 14px 12px 64px;position:relative;border-radius:6px;background:linear-gradient(100deg,var(--a),var(--b));transform:skewX(-6deg);box-shadow:0 4px 0 #0006;opacity:.72;transition:transform .15s,opacity .15s}
.mode i{position:absolute;left:14px;top:50%;transform:translateY(-50%) skewX(6deg);font-style:normal;font-size:30px}
.mode b{display:block;font-size:20px}
.mode span{display:block;font-size:12.5px;font-weight:400;font-style:normal;opacity:.92;margin-top:2px}
.mode.on{opacity:1;transform:skewX(-6deg) translateX(14px);outline:3px solid var(--banana)}
#playbox{position:absolute;right:28px;bottom:calc(28px + env(safe-area-inset-bottom,0px));text-align:right}
#modeName{font-size:18px;margin-bottom:8px;text-shadow:0 2px 6px #0008}
#go{background:linear-gradient(#ffe86b,#ffc400);color:#2a1c00;font:inherit;font-size:30px;padding:14px 64px;border:0;border-radius:6px;transform:skewX(-10deg);box-shadow:0 6px 0 #a67a00,0 12px 30px #0006;cursor:pointer}
#go:active{transform:skewX(-10deg) translateY(4px);box-shadow:0 2px 0 #a67a00}
#hint{position:absolute;left:24px;bottom:calc(20px + env(safe-area-inset-bottom,0px));max-width:min(520px,50vw);font-size:12px;font-weight:400;font-style:normal;line-height:1.5;opacity:.85;text-shadow:0 1px 4px #000}
.row label{display:block;font-size:14px;margin-bottom:6px;color:var(--banana)}
.opts{display:flex;gap:8px;flex-wrap:wrap}
.sw{width:30px;height:30px;border-radius:6px;border:2px solid #fff6;cursor:pointer;padding:0}
.pill{font:inherit;font-size:13px;padding:6px 12px;border-radius:6px;border:2px solid #fff4;background:#fff1;color:#fff;cursor:pointer}
.on.sw,.on.pill{outline:3px solid var(--banana);border-color:#fff}
#rps{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;background:#050a18d9;z-index:5;font-family:"Arial Black","Segoe UI",sans-serif;font-style:italic;color:#fff;text-align:center}
.rbox{padding:28px 36px;max-width:min(560px,92vw)}
.rbox h2{margin:0 0 10px;font-size:clamp(28px,6vw,46px);color:var(--banana);text-shadow:0 3px 0 #6b4a00}
#rt{font-size:18px;min-height:52px;margin:0 0 10px}
#rv{font-size:56px;min-height:72px}
#rb{display:flex;gap:14px;justify-content:center;margin-top:12px}
#rb button{font-size:46px;width:96px;height:96px;border-radius:14px;border:3px solid #fff5;background:#ffffff14;cursor:pointer}
#rb button:hover{background:#ffd93b33;border-color:var(--banana)}
</style>
</head>
<body>
<canvas id="c"></canvas>
<div id="hud">
  <div id="score"><div id="sb">0</div><div id="st">3:00</div><div id="so">0</div></div>
  <div id="msg"></div>
  <div id="boost"><b id="bn">100</b></div>
</div>
<div id="rps" class="hid"><div class="rbox"><h2>Sudden death</h2><p id="rt"></p><div id="rv"></div><div id="rb"><button data-m="0">✊</button><button data-m="1">✋</button><button data-m="2">✌️</button></div></div></div>
<div id="menu">
  <div class="top"><div class="logo">MONKEY<span>LEAGUE</span></div>
    <div class="tabs"><button class="tab on" data-t="pPlay">Play</button><button class="tab" data-t="pLock">Locker</button></div></div>
  <div id="pPlay"><div id="modes"></div>
    <div id="hint">Ground: W / S drive, A / D steer. Air: W / S pitch, A / D roll, Q / E yaw. Space jump (press again to flip), Shift boost, C ball cam, G grab (Bullet Ball), Esc menu.</div>
    <div id="playbox"><div id="modeName"></div><button id="go">Play</button></div></div>
  <div id="pLock" class="hid"></div>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
const R=3,AW=80,AL=120,GW=14,GH=10,CH=55,HR=16,HY=28,HZ=AL-12;
const cv=document.getElementById('c');
const rd=new THREE.WebGLRenderer({canvas:cv,antialias:true});
rd.setPixelRatio(Math.min(devicePixelRatio,1.5));
const sc=new THREE.Scene();sc.background=new THREE.Color(0x7fd0ff);sc.fog=new THREE.Fog(0xbfe6ff,350,1700);
const cam=new THREE.PerspectiveCamera(70,1,2,2500);
function rs(){rd.setSize(innerWidth,innerHeight,false);cam.aspect=innerWidth/innerHeight;cam.updateProjectionMatrix()}
addEventListener('resize',rs);rs();
sc.add(new THREE.HemisphereLight(0xffffff,0x2a5a2a,0.9));
const sun=new THREE.DirectionalLight(0xfff2c0,0.8);sun.position.set(30,80,20);sc.add(sun);

const M=(c,o)=>new THREE.MeshLambertMaterial(Object.assign({color:c},o||{}));
function box(w,h,d,m,x,y,z,p){const b=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),m);b.position.set(x,y,z);(p||sc).add(b);return b}
// arena
box(AW*2,0.5,AL*2,M(0x3f9b3f),0,-0.25,0);
for(let i=-9;i<=9;i++)box(AW*2,0.02,12,M(i%2?0x3a9238:0x46a544),0,0.01,i*12);
box(AW*2,0.03,0.4,M(0xffffff),0,0.03,0);
const ring=new THREE.Mesh(new THREE.RingGeometry(16,16.5,64),new THREE.MeshBasicMaterial({color:0xffffff,side:THREE.DoubleSide}));ring.rotation.x=-Math.PI/2;ring.position.y=0.04;sc.add(ring);
const glass=M(0xbfe8ff,{transparent:true,opacity:0.14});
box(1,CH,AL*2,glass,-AW-0.5,CH/2,0);box(1,CH,AL*2,glass,AW+0.5,CH/2,0);
const GG=new THREE.Group(),HG=new THREE.Group();sc.add(GG,HG);
[-1,1].forEach(s=>{
  const col=s<0?0x2f8cff:0xff8a1f;
  const sw=(AW-GW);
  box(sw,CH,1,glass,-(GW+sw/2),CH/2,s*(AL+0.5));box(sw,CH,1,glass,(GW+sw/2),CH/2,s*(AL+0.5));
  box(GW*2,CH-GH,1,glass,0,GH+(CH-GH)/2,s*(AL+0.5));
  const gm=M(col);
  box(1,GH,1,gm,-GW,GH/2,s*AL,GG);box(1,GH,1,gm,GW,GH/2,s*AL,GG);box(GW*2+1,1,1,gm,0,GH,s*AL,GG);
  box(GW*2,0.1,8,M(col,{transparent:true,opacity:0.5}),0,0.06,s*(AL+4),GG);
  box(GW*2,GH,0.5,M(col,{transparent:true,opacity:0.3}),0,GH/2,s*(AL+8),GG);
  box(GW*2,GH,1,glass,0,GH/2,s*(AL+0.5),HG);
  const hm=new THREE.Mesh(new THREE.TorusGeometry(HR,0.7,10,40),M(col,{emissive:col,emissiveIntensity:0.6}));hm.position.set(0,HY,s*HZ);hm.userData.ring=1;HG.add(hm);
  const hd=new THREE.Mesh(new THREE.CircleGeometry(HR-0.6,32),new THREE.MeshBasicMaterial({color:col,transparent:true,opacity:0.18,side:THREE.DoubleSide}));hd.position.set(0,HY,s*HZ);hd.userData.ring=1;HG.add(hd);
});
// outside the stadium
const SKYV='varying float h;void main(){h=normalize(position).y;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}';
const skyU={top:{value:new THREE.Color(0x3a8dde)},bot:{value:new THREE.Color(0xbfe6ff)}};
sc.add(new THREE.Mesh(new THREE.SphereGeometry(1800,24,16),new THREE.ShaderMaterial({uniforms:skyU,side:THREE.BackSide,depthWrite:false,vertexShader:SKYV,fragmentShader:'uniform vec3 top;uniform vec3 bot;varying float h;void main(){gl_FragColor=vec4(mix(bot,top,clamp(h*1.8,0.,1.)),1.);}'})));
const groundM=M(0x4a8b3f),ground=new THREE.Mesh(new THREE.PlaneGeometry(6000,6000),groundM);ground.rotation.x=-Math.PI/2;ground.position.y=-0.6;sc.add(ground);
const mg=new THREE.ConeGeometry(0.7,1,6,5);
{const pa=mg.attributes.position,cols=new Float32Array(pa.count*3),rock=new THREE.Color(0x5d6b8a),rock2=new THREE.Color(0x7a8aaa),snow=new THREE.Color(0xffffff);
 for(let i=0;i<pa.count;i++){const y=pa.getY(i)+0.5,c=y>0.72?snow:(y>0.55?rock2:rock);cols[i*3]=c.r;cols[i*3+1]=c.g;cols[i*3+2]=c.b}
 mg.setAttribute('color',new THREE.BufferAttribute(cols,3))}
const mmat=new THREE.MeshLambertMaterial({vertexColors:true,flatShading:true});
for(let i=0;i<28;i++){const a=i/28*Math.PI*2+Math.random()*.2,r=900+Math.random()*250,h=150+Math.random()*260;
  const m=new THREE.Mesh(mg,mmat);m.scale.set(h,h,h);m.rotation.y=Math.random()*6;m.position.set(Math.cos(a)*r,h/2-5,Math.sin(a)*r);m.matrixAutoUpdate=false;m.updateMatrix();sc.add(m)}
const cmat=new THREE.MeshBasicMaterial({color:0xffffff}),cg=new THREE.SphereGeometry(1,8,6);
for(let i=0;i<20;i++){const g=new THREE.Group(),a=Math.random()*Math.PI*2,r=300+Math.random()*900;
  for(let j=0;j<4;j++){const c=new THREE.Mesh(cg,cmat),s2=18+Math.random()*16;c.scale.set(s2,s2*0.5,s2);c.position.set(j*22-33,Math.random()*8,Math.random()*14);g.add(c)}
  g.position.set(Math.cos(a)*r,160+Math.random()*180,Math.sin(a)*r);g.rotation.y=Math.random()*3;g.matrixAutoUpdate=false;g.updateMatrix();sc.add(g)}
const stM=[M(0x3b3f4f),M(0x4a5068)],pal=[0xff4d6d,0xffd93b,0x2f8cff,0x35d07f,0xffffff,0xa56bff,0xff8a1f];
const crowd=new THREE.InstancedMesh(new THREE.BoxGeometry(1.2,1.8,1.2),new THREE.MeshLambertMaterial({color:0xffffff}),2400),cm4=new THREE.Matrix4(),cc=new THREE.Color();let cn=0;
function person(x,y,z){if(cn>=2400)return;cm4.setPosition(x,y,z);crowd.setMatrixAt(cn,cm4);crowd.setColorAt(cn,cc.setHex(pal[(Math.random()*pal.length)|0]));cn++}
for(let i=0;i<6;i++){const w=9,h=3*(i+1),off=AW+8+i*w,zz=AL+8+i*w;
  [-1,1].forEach(sg=>{
    box(w,h,(zz+w)*2,stM[i%2],sg*(off+w/2),h/2,0);
    for(let z=-zz;z<=zz;z+=3)person(sg*(off+w/2),h+0.9,z);
    box(AW*2,h,w,stM[i%2],0,h/2,sg*(zz+w/2));
    for(let x=-AW+2;x<=AW-2;x+=3)person(x,h+0.9,sg*(zz+w/2));
  })}
crowd.count=cn;crowd.frustumCulled=false;crowd.instanceMatrix.needsUpdate=true;if(crowd.instanceColor)crowd.instanceColor.needsUpdate=true;sc.add(crowd);
// trees for scenery
for(let i=0;i<40;i++){const a=i/40*Math.PI*2,r=300+(i%3)*14;const g=new THREE.Group();
  box(2,10,2,M(0x6b4a1c),0,5,0,g);const t=new THREE.Mesh(new THREE.SphereGeometry(7,10,8),M(0x2f8a3a));t.position.y=13;g.add(t);g.position.set(Math.cos(a)*r,0,Math.sin(a)*r*1.1);sc.add(g)}
// ball (coconut)
const ball={p:new THREE.Vector3(),v:new THREE.Vector3(),m:new THREE.Group()};
ball.m.add(new THREE.Mesh(new THREE.SphereGeometry(R,24,18),M(0x5a3a1e)));
for(let i=0;i<3;i++){const e=new THREE.Mesh(new THREE.CircleGeometry(0.5,10),M(0x1a0f06));const a=i*2.1;e.position.set(Math.cos(a)*R*0.8,R*0.55,Math.sin(a)*R*0.8);e.lookAt(0,0,0);e.rotateY(Math.PI);ball.m.add(e)}
const band=new THREE.Mesh(new THREE.TorusGeometry(R+0.02,0.18,6,30),M(0xffd93b));ball.m.add(band);
sc.add(ball.m);
// boost pads
const padPos=[[-60,-80],[60,-80],[-60,80],[60,80],[-72,0],[72,0],[0,-40],[0,40],[-40,0],[40,0]];
const pads=padPos.map(([x,z])=>{const m=new THREE.Mesh(new THREE.CylinderGeometry(2,2,0.2,16),M(0xffd93b,{emissive:0x806000}));m.position.set(x,0.12,z);sc.add(m);return{x,z,m,t:0}});
// cars
const BODY=['#2f8cff','#ff4d6d','#35d07f','#ffd93b','#a56bff','#ff8a1f','#f2f2f2','#222a33'];
const FUR=[['Chimp',0x8b5a2b],['Golden',0xd9a441],['Shadow',0x2b2b33],['Snow',0xeeeeee]];
const HATS=['None','Cap','Crown','Party'],WH=[0x222222,0xe8b923,0xd0342c,0xf0f0f0],TR=[['Fire',0xff7a1a],['Ice',0x59d7ff],['Magic',0xff5bd6],['Toxic',0x7dff3a]];
let cfg={b:0,f:0,h:0,w:0,t:0},mode='normal';
try{const sv=JSON.parse(localStorage.getItem('ml')||'{}');if(sv.cfg)Object.assign(cfg,sv.cfg);if(sv.mode)mode=sv.mode}catch(e){}
function mkCar(team,player,cf,par){
  const g=new THREE.Group(),tc=team?0xff8a1f:0x2f8cff,col=new THREE.Color(cf?BODY[cf.b]:tc);
  box(2.2,0.8,3.6,M(col),0,0,0,g);box(1.8,0.5,1.6,M(col),0,0.6,-0.3,g);
  box(1.9,0.15,0.5,M(tc,{emissive:tc,emissiveIntensity:0.6}),0,0.45,1.6,g);
  const wc=cf?WH[cf.w]:0x222222;
  [[-1.2,1.1],[1.2,1.1],[-1.2,-1.1],[1.2,-1.1]].forEach(([x,z])=>{const w=new THREE.Mesh(new THREE.CylinderGeometry(0.5,0.5,0.4,12),M(wc));w.rotation.z=Math.PI/2;w.position.set(x,-0.3,z);g.add(w)});
  const fur=cf?FUR[cf.f][1]:(player?0x8b5a2b:0x6b4a2c);
  const h=new THREE.Mesh(new THREE.SphereGeometry(0.7,14,10),M(fur));h.position.set(0,1.3,-0.2);g.add(h);
  [-0.75,0.75].forEach(x=>{const e=new THREE.Mesh(new THREE.SphereGeometry(0.3,8,6),M(0xd9a577));e.position.set(x,1.4,-0.2);g.add(e)});
  const f=new THREE.Mesh(new THREE.SphereGeometry(0.45,10,8),M(0xe8c39a));f.position.set(0,1.2,0.25);g.add(f);
  [-0.18,0.18].forEach(x=>{const e=new THREE.Mesh(new THREE.SphereGeometry(0.08,6,6),M(0x111111));e.position.set(x,1.4,0.58);g.add(e)});
  const hat=cf?cf.h:0;
  if(hat===1){const c=new THREE.Mesh(new THREE.CylinderGeometry(0.5,0.62,0.3,14),M(0xd0342c));c.position.set(0,1.98,-0.2);g.add(c);box(0.9,0.06,0.6,M(0xd0342c),0,1.9,0.5,g)}
  if(hat===2){const c=new THREE.Mesh(new THREE.CylinderGeometry(0.62,0.42,0.5,5),M(0xffc400));c.position.set(0,2.1,-0.2);g.add(c)}
  if(hat===3){const c=new THREE.Mesh(new THREE.ConeGeometry(0.4,1,10),M(0xff5bd6));c.position.set(0,2.4,-0.2);g.add(c)}
  const fl=new THREE.Mesh(new THREE.ConeGeometry(0.55,2.2,10),new THREE.MeshBasicMaterial({color:cf?TR[cf.t][1]:0xff7a1a}));
  fl.rotation.x=-Math.PI/2;fl.position.set(0,0,-2.9);fl.visible=false;g.add(fl);g.userData.fl=fl;
  (par||sc).add(g);return g}
const cars=[];
[[0,0,1],[0,1,0],[1,0,0],[1,1,0]].forEach(([t,i,pl])=>{const c={team:t,idx:i,player:!!pl,m:mkCar(t,pl,pl?cfg:null),x:0,y:.8,z:0,yaw:0,vx:0,vy:0,vz:0,boost:33,jumps:0,jt:0,boosting:false};cars.push(c)});
function spawn(){
  const sp=[[0,-AL*0.6],[-28,-AL*0.38],[0,AL*0.6],[28,AL*0.38]];
  cars.forEach((c,i)=>{c.x=sp[i][0];c.z=sp[i][1];c.y=.8;c.vx=c.vy=c.vz=0;c.boost=33;c.jumps=0;c.s=0;c.dodgeT=0;c.alive=true;c.m.visible=true;c.ceilT=0;c.noStick=0;c.surfT=0;c.jt=0;
    c.f=new THREE.Vector3(0,0,c.team?-1:1);c.fv=c.f.clone();c.u=new THREE.Vector3(0,1,0)});
  ball.p.set(0,R,0);ball.v.set(0,0,0);
}
// input
const K={};let jumpP=false;
addEventListener('keydown',e=>{if(e.repeat)return;K[e.code]=1;if(e.code==='Space'){jumpP=true;e.preventDefault()}if(e.code==='KeyC')ballCam=!ballCam});
addEventListener('keyup',e=>{K[e.code]=0});
let ballCam=true;
// game state
let st='menu',timer=180,cd=3,score=[0,0],ot=false,goalT=0;
const $=id=>document.getElementById(id);
function openMenu(){$('rps').classList.add('hid');menuOpen=true;st='menu';$('menu').style.display='block';$('hud').style.display='none';$('msg').textContent=''}
function startGame(){applyMode();rebuildPlayer();score=[0,0];timer=180;ot=false;otT=0;spawn();bulletInit();st='count';cd=3;menuOpen=false;$('menu').style.display='none';$('hud').style.display='block'}
function rebuildPlayer(){const c=cars[0];sc.remove(c.m);c.m=mkCar(0,true,cfg,sc)}
function applyMode(){
  const th={normal:[0x3a8dde,0xbfe6ff,0x4a8b3f],zerog:[0x0b0620,0x4b2a8a,0x24164d],hoops:[0xff7a3d,0xffd9a0,0x9a6b3a],bullet:[0x2a0a3d,0xff7a4d,0x2b1a3a]}[mode];
  skyU.top.value.set(th[0]);skyU.bot.value.set(th[1]);sc.fog.color.set(th[1]);sc.background=new THREE.Color(th[1]);groundM.color.setHex(th[2]);
  GG.visible=mode==='normal'||mode==='zerog';HG.visible=mode==='hoops'||mode==='bullet';HG.children.forEach(o=>{if(o.userData.ring)o.visible=mode==='hoops'});
}
function hoopCollide(){
  const p=ball.p,v=ball.v;
  for(const sg of[-1,1]){
    const dx=p.x,dy=p.y-HY,rl=Math.hypot(dx,dy)||1e-6;
    const nx=p.x-HR*dx/rl,ny=p.y-(HY+HR*dy/rl),nz=p.z-sg*HZ,d=Math.hypot(nx,ny,nz);
    if(d<R+0.7&&d>1e-6){const k=(R+0.7-d)/d;p.x+=nx*k;p.y+=ny*k;p.z+=nz*k;const vn=(v.x*nx+v.y*ny+v.z*nz)/d;if(vn<0){const q=1.7*vn/d;v.x-=q*nx;v.y-=q*ny;v.z-=q*nz}}
  }
}
let menuOpen=true;
addEventListener('keydown',e=>{if(e.code==='Escape'&&!menuOpen)openMenu()});
const NR=[[0,1,0],[0,-1,0],[1,0,0],[-1,0,0],[0,0,1],[0,0,-1]],AX=[1,1,0,0,2,2],SO=[0.8,CH-0.8,-AW+0.8,AW-0.8,-AL+0.8,AL-0.8];
const cl=(v,a,b)=>Math.max(a,Math.min(b,v));
const dot=(a,b)=>a[0]*b[0]+a[1]*b[1]+a[2]*b[2];
const crs=(a,b)=>[a[1]*b[2]-a[2]*b[1],a[2]*b[0]-a[0]*b[2],a[0]*b[1]-a[1]*b[0]];
const nrm=a=>{const l=Math.hypot(a[0],a[1],a[2])||1;return[a[0]/l,a[1]/l,a[2]/l]};
const sDist=(s,P)=>NR[s][AX[s]]*(P[AX[s]]-SO[s]);
const allowed=(c,s,P)=>s===1?c.noStick<=0:s>=4?(mode==='hoops'||mode==='bullet'||!(Math.abs(P[0])<GW-1.5&&P[1]<GH-0.8)):true;
function tang(F,k){const fn=dot(F,k);let T=[F[0]-fn*k[0],F[1]-fn*k[1],F[2]-fn*k[2]];
  if(Math.hypot(T[0],T[1],T[2])<0.05){const g=Math.abs(k[1])<0.9?[0,1,0]:[0,0,1],gn=dot(g,k);T=[g[0]-gn*k[0],g[1]-gn*k[1],g[2]-gn*k[2]]}
  return nrm(T)}
// c.s = surface the car is driving on: 0 floor, 1 ceiling, 2/3 side walls, 4/5 end walls, -1 airborne
function carStep(c,inp,dt){
  const P=[c.x,c.y,c.z],V=[c.vx,c.vy,c.vz];let F=[c.f.x,c.f.y,c.f.z],U=null;
  c.boosting=false;c.jt+=dt;if(c.dodgeT>0)c.dodgeT-=dt;if(c.noStick>0)c.noStick-=dt;
  const wantBoost=inp.boost&&c.boost>0;let jumped=false;
  if(c.s<0){
    for(let s=0;s<6;s++){
      if(!allowed(c,s,P))continue;const k=NR[s];
      if(sDist(s,P)<=0&&dot(V,k)<=0.5){const vn=dot(V,k);for(let i=0;i<3;i++)V[i]-=vn*k[i];c.s=s;c.jumps=0;c.jt=0;c.ceilT=0;break}
    }
  }
  if(c.s>=0){
    const s=c.s,k=NR[s];
    F=tang(F,k);let L=crs(k,F);
    let fs=dot(V,F),ls=dot(V,L);
    if(inp.thr)fs+=inp.thr*((inp.thr*fs<0)?70:34)*dt;else fs*=Math.exp(-1.3*dt);
    if(wantBoost){fs+=52*dt;c.boost-=34*dt;c.boosting=true}
    ls*=Math.exp(-9*dt);
    fs=Math.max(-20,Math.min(c.boosting?55:30,fs));
    const a=inp.steer*dt*3.1/(1+Math.abs(fs)/22)*(fs>=0?1:-1)*Math.min(1,Math.abs(fs)/5),ca=Math.cos(a),sa=Math.sin(a);
    F=nrm([F[0]*ca+L[0]*sa,F[1]*ca+L[1]*sa,F[2]*ca+L[2]*sa]);L=crs(k,F);
    for(let i=0;i<3;i++)V[i]=F[i]*fs+L[i]*ls;
    if(s>=2&&mode!=='zerog')V[1]-=14*dt;
    if(s===1&&mode!=='zerog'){c.ceilT+=dt;if(c.ceilT>0.35){c.s=-1;c.noStick=2.2;V[1]=-4}}
    if(inp.jumpP&&c.s>=0){for(let i=0;i<3;i++)V[i]+=k[i]*20;c.s=-1;c.jumps=1;c.jt=0;jumped=true;if(s===1)c.noStick=2.2}
  }
  if(c.s<0){
    if(c.player||mode==='zerog'||mode==='hoops'){
      U=[c.u.x,c.u.y,c.u.z];
      const pt=-inp.thr*5.5*dt,rl=-inp.steer*6.5*dt,yw=(inp.yaw||0)*4.5*dt;
      let cs=Math.cos(pt),sn=Math.sin(pt);
      [F,U]=[[F[0]*cs+U[0]*sn,F[1]*cs+U[1]*sn,F[2]*cs+U[2]*sn],[U[0]*cs-F[0]*sn,U[1]*cs-F[1]*sn,U[2]*cs-F[2]*sn]];
      let L=crs(U,F);cs=Math.cos(rl);sn=Math.sin(rl);
      U=[U[0]*cs-L[0]*sn,U[1]*cs-L[1]*sn,U[2]*cs-L[2]*sn];
      L=crs(U,F);cs=Math.cos(yw);sn=Math.sin(yw);
      F=nrm([F[0]*cs+L[0]*sn,F[1]*cs+L[1]*sn,F[2]*cs+L[2]*sn]);
      const d=dot(U,F);U=nrm([U[0]-d*F[0],U[1]-d*F[1],U[2]-d*F[2]]);
    }else{
      const a=inp.steer*4*dt,ca=Math.cos(a),sa=Math.sin(a);
      F=[F[0]*ca+F[2]*sa,F[1]*Math.exp(-2*dt),F[2]*ca-F[0]*sa];
      if(Math.hypot(F[0],F[2])<0.25){const vm=Math.hypot(V[0],V[2]);F=vm>1?[V[0]/vm*0.5,F[1],V[2]/vm*0.5]:[0,F[1],0.5]}
      F=nrm(F);
    }
    if(wantBoost){for(let i=0;i<3;i++)V[i]+=F[i]*70*dt;c.boost-=34*dt;c.boosting=true}
    if(inp.jumpP&&!jumped&&c.jumps===1&&c.jt<1.4){c.jumps=2;const dd=(mode==='bullet'&&inp.thr<0)?-1:1;V[0]+=F[0]*14*dd;V[2]+=F[2]*14*dd;V[1]=Math.max(V[1],4);c.dodgeT=0.45;c.dodgeDir=dd}
    const kd=Math.exp(-0.15*dt);V[0]*=kd;V[2]*=kd;if(mode==='zerog')V[1]*=kd;
    V[1]-=(mode==='zerog'?0:(c.noStick>0?80:30))*dt;
    const hs=Math.hypot(V[0],V[2]);if(hs>55){V[0]*=55/hs;V[2]*=55/hs}if(V[1]>55)V[1]=55;if(mode==='zerog'){const sp=Math.hypot(V[0],V[1],V[2]);if(sp>55)for(let i=0;i<3;i++)V[i]*=55/sp}
  }
  if(c.boost<0)c.boost=0;if(mode==='zerog')c.boost=100;
  P[0]+=V[0]*dt;P[1]+=V[1]*dt;P[2]+=V[2]*dt;
  if(c.s>=0){
    const s=c.s,k=NR[s];P[AX[s]]=SO[s];
    for(let s2=0;s2<6;s2++){
      if(s2===s||!allowed(c,s2,P)||sDist(s2,P)>0)continue;
      const k2=NR[s2];
      if(-dot(V,k2)>0){
        const rot=X=>{const b=dot(X,k2);return[X[0]-b*k2[0]-b*k[0],X[1]-b*k2[1]-b*k[1],X[2]-b*k2[2]-b*k[2]]};
        const nv=rot(V);V[0]=nv[0];V[1]=nv[1];V[2]=nv[2];F=rot(F);
        c.s=s2;P[AX[s2]]=SO[s2];c.ceilT=0;break;
      }else P[AX[s2]]=SO[s2];
    }
    if(c.s>=4&&!allowed(c,c.s,P))c.s=-1;
  }
  if(P[1]>CH-0.8){P[1]=CH-0.8;if(V[1]>0)V[1]=0}
  P[0]=cl(P[0],-AW+0.8,AW-0.8);
  const open=mode!=='hoops'&&mode!=='bullet'&&Math.abs(P[0])<GW-1.5&&P[1]<GH-0.8,zl=open?AL+7:AL-0.8;P[2]=cl(P[2],-zl,zl);
  if(Math.abs(P[2])>AL-0.8)P[0]=cl(P[0],-(GW-1.5),GW-1.5);
  if(c.s===0)pads.forEach(p=>{if(p.t<=0&&Math.hypot(p.x-P[0],p.z-P[2])<3.5){c.boost=Math.min(100,c.boost+(Math.abs(p.x)>=60?100:40));p.t=4}});
  c.surfT=c.s>0?c.surfT+dt:0;
  c.x=P[0];c.y=P[1];c.z=P[2];c.vx=V[0];c.vy=V[1];c.vz=V[2];c.f.set(F[0],F[1],F[2]);
  if(U){c.u.set(U[0],U[1],U[2]);c.fv.copy(c.f)}else{
    const tu=c.s>=0?NR[c.s]:[0,1,0],T=new THREE.Vector3(tu[0],tu[1],tu[2]);
    if(c.u.dot(T)<-0.95)c.u.addScaledVector(c.fv,0.3);
    c.u.lerp(T,Math.min(1,12*dt)).normalize();
    c.fv.copy(c.f).addScaledVector(c.u,-c.f.dot(c.u));
  }
  if(c.fv.lengthSq()<1e-4)c.fv.set(0,0,1).addScaledVector(c.u,-c.u.z);
  c.fv.normalize();
  const l=new THREE.Vector3().crossVectors(c.u,c.fv);
  if(c.m.userData.fl){c.m.userData.fl.visible=c.boosting;c.m.userData.fl.scale.y=0.8+Math.random()*0.5}c.m.position.set(c.x,c.y,c.z);c.m.quaternion.setFromRotationMatrix(new THREE.Matrix4().makeBasis(l,c.u,c.fv));
}
function pilot(c){
  const sgn=c.team?-1:1,mate=cars.find(o=>o!==c&&o.team===c.team);
  const dB=Math.hypot(ball.p.x-c.x,ball.p.y-c.y,ball.p.z-c.z),dM=Math.hypot(ball.p.x-mate.x,ball.p.y-mate.y,ball.p.z-mate.z);
  const chase=dB<dM||(dB===dM&&c.idx<mate.idx),gp=mode==='hoops'?[0,HY,sgn*HZ]:[0,6,sgn*AL];
  let tx,ty,tz;
  if(chase){const gx=gp[0]-ball.p.x,gy=gp[1]-ball.p.y,gz=gp[2]-ball.p.z,gl=Math.hypot(gx,gy,gz)||1,lead=dB/70,o=R+2.4;
    tx=ball.p.x+ball.v.x*lead-gx/gl*o;ty=ball.p.y+ball.v.y*lead-gy/gl*o;tz=ball.p.z+ball.v.z*lead-gz/gl*o}
  else{tx=ball.p.x*0.4;ty=Math.max(8,ball.p.y*0.6);tz=-sgn*(AL-30)+ball.p.z*0.3}
  let dx=tx-c.x,dy=ty-c.y,dz=tz-c.z;const dl=Math.hypot(dx,dy,dz)||1;dx/=dl;dy/=dl;dz/=dl;
  const f=c.f,u=c.u,L=new THREE.Vector3().crossVectors(u,f);
  const dL=dx*L.x+dy*L.y+dz*L.z,dU=dx*u.x+dy*u.y+dz*u.z,dF=dx*f.x+dy*f.y+dz*f.z;
  return{thr:-cl(dU*3+(dF<0?Math.sign(dU||1):0),-1,1),steer:0,yaw:cl(dL*3+(dF<0?Math.sign(dL||1):0),-1,1),boost:dF>0.85&&dl>6,jumpP:false};
}
function steerTo(c,tx,tz){const yaw=Math.atan2(c.f.x,c.f.z);let df=Math.atan2(tx-c.x,tz-c.z)-yaw;while(df>Math.PI)df-=2*Math.PI;while(df<-Math.PI)df+=2*Math.PI;return{df,thr:Math.abs(df)>2.4?-1:1,steer:cl(df*2.5,-1,1)}}
function bulletBot(c){
  const B=ball,me=cars.indexOf(c),t=B.tgt===me,dx=B.p.x-c.x,dz=B.p.z-c.z,dy=B.p.y-c.y,d3=Math.hypot(dx,dy,dz);
  let tx,tz,jump=false,boost=false;
  if(t){
    tx=cl(c.x-dx*2,-AW+15,AW-15);tz=cl(c.z-dz*2,-AL+15,AL-15);boost=d3>22;
    if(c.s===0&&d3<26&&c.jumps===0){jump=true;c.will=Math.random()<0.6}
    if(c.jumps===1&&c.will&&d3<12&&c.jt>0.12)jump=true;
    if(c.jumps===1&&d3<16){tx=B.p.x;tz=B.p.z}
  }else{
    if(c.wx===undefined||Math.hypot(c.wx-c.x,c.wz-c.z)<8){c.wx=(Math.random()-.5)*AW*1.5;c.wz=(Math.random()-.5)*AL*1.5}
    tx=c.wx;tz=c.wz;if(d3<28){tx=cl(c.x-dx*2,-AW+15,AW-15);tz=cl(c.z-dz*2,-AL+15,AL-15)}
  }
  const q=steerTo(c,tx,tz);if(c.s>0&&c.surfT>0.3)jump=true;
  return{thr:(t&&c.jumps===1)?1:q.thr,steer:q.steer,boost:boost&&Math.abs(q.df)<0.4,jumpP:jump};
}
function botInput(c){
  if(mode==='bullet')return bulletBot(c);
  if(mode==='zerog'){if(c.s>=0)return{thr:0,steer:0,boost:false,jumpP:true};return pilot(c)}
  if(mode==='hoops'&&c.s<0)return pilot(c);
  const sgn=c.team?-1:1,mate=cars.find(o=>o!==c&&o.team===c.team),yaw=Math.atan2(c.f.x,c.f.z);
  const dB=Math.hypot(ball.p.x-c.x,ball.p.z-c.z),dM=Math.hypot(ball.p.x-mate.x,ball.p.z-mate.z);
  const chase=dB<dM||(dB===dM&&c.idx<mate.idx);
  let tx,tz,boost=false;
  if(chase){
    const gx=0-ball.p.x,gz=sgn*AL-ball.p.z,gl=Math.hypot(gx,gz)||1;
    if((c.z-ball.p.z)*sgn>3){tx=ball.p.x*0.6;tz=ball.p.z-sgn*16}
    else{tx=ball.p.x-gx/gl*3.5;tz=ball.p.z-gz/gl*3.5;boost=dB>16}
  }else{tx=ball.p.x*0.4;tz=-sgn*(AL-20)+ball.p.z*0.25}
  const dx=tx-c.x,dz=tz-c.z,ang=Math.atan2(dx,dz);
  let df=ang-yaw;while(df>Math.PI)df-=2*Math.PI;while(df<-Math.PI)df+=2*Math.PI;
  const dist=Math.hypot(dx,dz);
  let thr=Math.abs(df)>2.4?-1:1;if(!chase&&dist<4)thr=0;
  let jump=false;
  if(chase&&c.s===0&&((mode==='hoops'&&dB<30&&ball.p.y>6)||(dB<12&&(ball.p.y>5||Math.random()<0.004))))jump=true;
  else if(c.jumps===1&&dB<9&&c.jt>0.15&&c.jt<0.6)jump=true;
  if(c.s>0&&c.surfT>0.3)jump=true;
  return{thr,steer:Math.max(-1,Math.min(1,df*2.5)),boost:boost&&Math.abs(df)<0.3,jumpP:jump};
}
function ballCollide(c){
  const u=c.u,f=c.fv,l=new THREE.Vector3().crossVectors(u,f);
  const d=ball.p.clone().sub(new THREE.Vector3(c.x,c.y,c.z));
  const a=d.dot(l),b=d.dot(u),e=d.dot(f);
  let ex=a-cl(a,-1.1,1.1),ey=b-cl(b,-0.5,1.1),ez=e-cl(e,-1.8,1.8),dist=Math.hypot(ex,ey,ez);
  if(dist>=R)return;
  if(dist<1e-4){ex=0;ey=1;ez=0;dist=1}
  ex/=dist;ey/=dist;ez/=dist;
  const n=new THREE.Vector3().addScaledVector(l,ex).addScaledVector(u,ey).addScaledVector(f,ez);
  ball.p.addScaledVector(n,R-dist);
  const vn=ball.v.clone().sub(new THREE.Vector3(c.vx,c.vy,c.vz)).dot(n);
  if(vn<0){
    n.y+=0.18;n.normalize();
    const j=-1.6*vn+0.28*Math.hypot(c.vx,c.vy,c.vz);
    ball.v.addScaledVector(n,j);
    const bs=ball.v.length();if(bs>75)ball.v.multiplyScalar(75/bs);
  }
}
function ballStep(dt){
  const p=ball.p,v=ball.v;
  v.y-=(mode==='zerog'||mode==='bullet'?0:22)*dt;v.multiplyScalar(1-0.02*dt);p.addScaledVector(v,dt);
  if(p.y<R){p.y=R;if(v.y<0)v.y=-v.y*0.62;if(Math.abs(v.y)<1.2)v.y=0;v.x*=1-0.35*dt;v.z*=1-0.35*dt}
  if(p.y>CH-R){p.y=CH-R;if(v.y>0)v.y=-v.y*0.6}
  const inGoalY=p.y<GH-0.3;
  const inMouth=mode!=='hoops'&&mode!=='bullet'&&Math.abs(p.x)<GW&&inGoalY;
  if(Math.abs(p.z)>AL-R&&!inMouth){
    if(mode!=='hoops'&&mode!=='bullet'&&Math.abs(p.z)<AL+0.5&&Math.abs(p.x)>=GW-R&&Math.abs(p.x)<GW+R&&inGoalY){
      p.x=Math.sign(p.x)*(GW+R);v.x=Math.abs(v.x)*Math.sign(p.x)*0.5;
    }else{p.z=Math.sign(p.z)*(AL-R);v.z=-v.z*0.7}
  }
  if(Math.abs(p.z)>AL-1&&Math.abs(p.x)>GW-R&&inGoalY&&Math.abs(p.x)<GW+R*2){ /* post push */ }
  if(Math.abs(p.z)>AL){p.x=Math.max(-GW+R,Math.min(GW-R,p.x));if(p.y>GH-R){p.y=GH-R;if(v.y>0)v.y=-v.y*0.5}}
  if(Math.abs(p.z)>AL+8-R){p.z=Math.sign(p.z)*(AL+8-R);v.z=-v.z*0.5}
  if(p.x>AW-R){p.x=AW-R;v.x=-v.x*0.7}if(p.x<-AW+R){p.x=-AW+R;v.x=-v.x*0.7}if(mode==='hoops')hoopCollide();
}
function step(dt){
  pads.forEach(p=>{if(p.t>0)p.t-=dt;p.m.visible=p.t<=0&&mode!=='zerog'});
  if(st==='count'){cd-=dt;if(cd<=0){st='play';$('msg').textContent=''}else $('msg').textContent=Math.ceil(cd);return}
  if(st==='goal'){goalT-=dt;ballStep(dt);if(goalT<=0){if(ot&&score[0]!==score[1]){over();return}spawn();st='count';cd=3}return}
  if(st!=='play')return;
  if(mode==='bullet'){bulletStep(dt);return}
  if(ot){otT+=dt;if(otT>=60){startRps();return}}
  if(!ot){timer-=dt;if(timer<=0){timer=0;if(score[0]===score[1]){ot=true;otT=0;$('msg').textContent='Golden goal';setTimeout(()=>{if(st==='play')$('msg').textContent=''},1800)}else{over();return}}}
  cars.forEach(c=>{
    const inp=c.player?{thr:(K.KeyW||K.ArrowUp?1:0)-(K.KeyS||K.ArrowDown?1:0),steer:(K.KeyA||K.ArrowLeft?1:0)-(K.KeyD||K.ArrowRight?1:0),boost:!!(K.ShiftLeft||K.ShiftRight),yaw:(K.KeyQ?1:0)-(K.KeyE?1:0),jumpP}:botInput(c);
    carStep(c,inp,dt)});
  jumpP=false;
  for(let i=0;i<4;i++)for(let j=i+1;j<4;j++){const a=cars[i],b=cars[j],dx=b.x-a.x,dz=b.z-a.z,d=Math.hypot(dx,dz);
    if(d<3.4&&d>0.01&&Math.abs(a.y-b.y)<1.5){const o=(3.4-d)/2,nx=dx/d,nz=dz/d;a.x-=nx*o;a.z-=nz*o;b.x+=nx*o;b.z+=nz*o;const rv=(b.vx-a.vx)*nx+(b.vz-a.vz)*nz;if(rv<0){a.vx+=nx*rv*0.5;a.vz+=nz*rv*0.5;b.vx-=nx*rv*0.5;b.vz-=nz*rv*0.5}}}
  const pz=ball.p.z;ballStep(dt);cars.forEach(ballCollide);
  let t=-1;
  if(mode==='hoops'){for(const sg of[-1,1]){const hz=sg*HZ;if((pz-hz)*(ball.p.z-hz)<=0&&pz!==ball.p.z&&Math.sign(ball.v.z)===sg&&Math.hypot(ball.p.x,ball.p.y-HY)<HR)t=sg>0?0:1}}
  else if(Math.abs(ball.p.z)>AL+R*0.9&&Math.abs(ball.p.x)<GW)t=ball.p.z>0?0:1;
  if(t>=0){score[t]++;st='goal';goalT=3;$('msg').textContent=(t?'Orange':'Blue')+' scores!'}
}
function over(w){st='over';if(!w)w=(score[0]>score[1]?'Blue':'Orange')+' wins!';$('msg').textContent=w;setTimeout(openMenu,2800)}
// camera + render
const camP=new THREE.Vector3(0,12,-90),camL=new THREE.Vector3();
function updCam(dt){
  const me=cars[0];let tx,ty,tz,lx,ly,lz;
  if(ballCam){
    let dx=ball.p.x-me.x,dz=ball.p.z-me.z;const l=Math.hypot(dx,dz)||1;dx/=l;dz/=l;
    tx=me.x-dx*17;ty=Math.max(8,me.y+6)+Math.min(8,ball.p.y*0.3);tz=me.z-dz*17;lx=ball.p.x;ly=ball.p.y*0.6+1;lz=ball.p.z;
  }else{
    let fx=me.f.x,fz=me.f.z;const l=Math.hypot(fx,fz)||1;fx/=l;fz/=l;
    tx=me.x-fx*16;ty=Math.max(7,me.y+5.5);tz=me.z-fz*16;lx=me.x+fx*12;ly=me.y+1;lz=me.z+fz*12;
  }
  tx=cl(tx,-AW+1,AW-1);ty=Math.min(ty,CH-1);tz=cl(tz,-AL-6,AL+6);
  const k=1-Math.exp(-6*dt);camP.x+=(tx-camP.x)*k;camP.y+=(ty-camP.y)*k;camP.z+=(tz-camP.z)*k;
  camL.x+=(lx-camL.x)*k*1.5;camL.y+=(ly-camL.y)*k*1.5;camL.z+=(lz-camL.z)*k*1.5;
  cam.position.copy(camP);cam.lookAt(camL);
}
// ---------- lobby ----------
const lob=new THREE.Scene(),lcam=new THREE.PerspectiveCamera(38,1,0.5,3000),islands=[];let prev=null;
const MODES=[
 {id:'normal',name:'Classic',desc:'2v2 car soccer. Drive the walls and ceiling.',ico:'\u26bd',a:'#2f8cff',b:'#6a5cff'},
 {id:'zerog',name:'Zero Gravity',desc:'No gravity. Infinite boost. Float anywhere.',ico:'\ud83d\ude80',a:'#7b2ff7',b:'#f107a3'},
 {id:'hoops',name:'Hoops',desc:'Fly the ball through the big hoop. Aerials only.',ico:'\ud83c\udfc0',a:'#ff8a1f',b:'#ff3d5a'},
 {id:'bullet',name:'Bullet Ball',desc:'Free-for-all. A homing ball hunts one car. Dodge forward to parry, backflip to freeze.',ico:'\u2604',a:'#e8262d',b:'#7a1fa2'}];
const hx=n=>'#'+n.toString(16).padStart(6,'0');
function save(){try{localStorage.setItem('ml',JSON.stringify({cfg,mode}))}catch(e){}}
function rebuildPrev(){if(prev)lob.remove(prev);prev=mkCar(0,true,cfg,lob);prev.userData.fl.visible=true;prev.scale.setScalar(1.6);prev.position.set(0,1.3,0)}
function lobbyInit(){
  lob.add(new THREE.HemisphereLight(0xffffff,0x7a5cff,1.0));const dl=new THREE.DirectionalLight(0xfff0c0,1.2);dl.position.set(6,10,8);lob.add(dl);
  lob.add(new THREE.Mesh(new THREE.SphereGeometry(1200,24,16),new THREE.ShaderMaterial({side:THREE.BackSide,depthWrite:false,vertexShader:SKYV,fragmentShader:'varying float h;void main(){vec3 a=vec3(.36,.18,.85),b=vec3(.18,.62,1.),c=vec3(1.,.8,.45);float t=h*1.3+.25;vec3 col=t<.35?mix(c,b,smoothstep(0.,.35,t)):mix(b,a,smoothstep(.35,1.,t));gl_FragColor=vec4(col,1.);}'})));
  const pod=new THREE.Mesh(new THREE.CylinderGeometry(3.4,3.9,0.7,40),M(0x252a63));pod.position.y=-0.35;lob.add(pod);
  const rg=new THREE.Mesh(new THREE.TorusGeometry(3.45,0.13,8,48),new THREE.MeshBasicMaterial({color:0xffd93b}));rg.rotation.x=Math.PI/2;rg.position.y=0.02;lob.add(rg);
  [[-16,-3,-14,1],[18,2,-20,1.3],[8,-8,-34,1.8],[-26,6,-40,1.6],[30,-4,-48,2]].forEach(([x,y,z,sc2],i)=>{
    const g=new THREE.Group();g.add(new THREE.Mesh(new THREE.CylinderGeometry(4,4,0.8,16),M(0x4fc46a)));
    const bt=new THREE.Mesh(new THREE.ConeGeometry(4,5,10),M(0x7a5236));bt.rotation.x=Math.PI;bt.position.y=-2.9;g.add(bt);
    const tr=new THREE.Mesh(new THREE.SphereGeometry(1.4,10,8),M(0x2f9a4a));tr.position.y=2.4;g.add(tr);box(0.5,1.6,0.5,M(0x6b4a1c),0,1.2,0,g);
    g.position.set(x,y,z);g.scale.setScalar(sc2);g.userData.y=y;lob.add(g);islands.push(g)});
  for(let i=0;i<14;i++){const c=new THREE.Mesh(new THREE.SphereGeometry(3+Math.random()*3,10,8),new THREE.MeshBasicMaterial({color:0xffffff,transparent:true,opacity:0.85}));c.scale.y=0.5;c.position.set(-60+Math.random()*120,-14+Math.random()*10,-20-Math.random()*60);lob.add(c)}
  const pts=new Float32Array(300*3);for(let i=0;i<300;i++){pts[i*3]=(Math.random()-.5)*60;pts[i*3+1]=Math.random()*24-4;pts[i*3+2]=-Math.random()*40+8}
  const pg=new THREE.BufferGeometry();pg.setAttribute('position',new THREE.BufferAttribute(pts,3));lob.add(new THREE.Points(pg,new THREE.PointsMaterial({color:0xffe27a,size:0.25})));
  // UI
  const modes=$('modes');
  MODES.forEach(m=>{const d=document.createElement('button');d.className='mode';d.style.setProperty('--a',m.a);d.style.setProperty('--b',m.b);
    d.innerHTML='<i>'+m.ico+'</i><b>'+m.name+'</b><span>'+m.desc+'</span>';d.onclick=()=>{mode=m.id;save();selMode()};modes.appendChild(d);m.el=d});
  function selMode(){MODES.forEach(m=>m.el.classList.toggle('on',m.id===mode));$('modeName').textContent=MODES.find(m=>m.id===mode).name}
  if(!MODES.some(m=>m.id===mode))mode='normal';selMode();
  const rows=[['Body','b',BODY.map(c=>({c}))],['Monkey','f',FUR.map(f=>({c:hx(f[1]),n:f[0]}))],['Hat','h',HATS.map(n=>({n}))],['Wheels','w',WH.map(c=>({c:hx(c)}))],['Boost trail','t',TR.map(t=>({c:hx(t[1]),n:t[0]}))]];
  rows.forEach(([lab,key,items])=>{
    const r=document.createElement('div');r.className='row';r.innerHTML='<label>'+lab+'</label>';const o=document.createElement('div');o.className='opts';r.appendChild(o);
    const bs=items.map((it,i)=>{const b=document.createElement('button');if(it.c){b.className='sw';b.style.background=it.c;b.title=it.n||''}else{b.className='pill';b.textContent=it.n}
      b.onclick=()=>{cfg[key]=i;save();rebuildPrev();mark()};o.appendChild(b);return b});
    function mark(){bs.forEach((b,i)=>b.classList.toggle('on',cfg[key]===i))}mark();$('pLock').appendChild(r)});
  document.querySelectorAll('.tab').forEach(t=>t.onclick=()=>{document.querySelectorAll('.tab').forEach(x=>x.classList.toggle('on',x===t));['pPlay','pLock'].forEach(id=>$(id).classList.toggle('hid',id!==t.dataset.t))});
  $('go').onclick=startGame;$('hud').style.display='none';rebuildPrev();applyMode();
}
function animLobby(dt,t){
  lcam.aspect=innerWidth/innerHeight;lcam.updateProjectionMatrix();const pt=lcam.aspect<1;
  lcam.position.set(pt?0:-2.2,pt?4.5:2.8,pt?17:11.5);lcam.lookAt(pt?0:-2.2,1.5,0);
  if(prev){prev.rotation.y+=dt*0.6;prev.userData.fl.scale.y=0.85+Math.sin(t*30)*0.2}
  islands.forEach((g,i)=>g.position.y=g.userData.y+Math.sin(t*0.8+i)*0.6);
}
// ---------- bullet ball ----------
const bm=new THREE.Group(),fx=[];
bm.add(new THREE.Mesh(new THREE.SphereGeometry(R,20,14),new THREE.MeshBasicMaterial({color:0xb04dff})));
bm.add(new THREE.Mesh(new THREE.SphereGeometry(R*1.4,16,12),new THREE.MeshBasicMaterial({color:0xff4dd2,transparent:true,opacity:0.25})));
bm.visible=false;sc.add(bm);
const tm=new THREE.Mesh(new THREE.ConeGeometry(1.2,2.6,8),new THREE.MeshBasicMaterial({color:0xff2d2d}));tm.rotation.x=Math.PI;tm.visible=false;sc.add(tm);
let endT=-1;
function flash(t){$('msg').textContent=t;clearTimeout(flash.h);flash.h=setTimeout(()=>{if(st==='play')$('msg').textContent=''},1400)}
function boom(x,y,z){const m=new THREE.Mesh(new THREE.SphereGeometry(2,12,10),new THREE.MeshBasicMaterial({color:0xffa030,transparent:true,opacity:0.9}));m.position.set(x,y,z);sc.add(m);fx.push({m,t:0})}
function playerInp(){return{thr:(K.KeyW||K.ArrowUp?1:0)-(K.KeyS||K.ArrowDown?1:0),steer:(K.KeyA||K.ArrowLeft?1:0)-(K.KeyD||K.ArrowRight?1:0),boost:!!(K.ShiftLeft||K.ShiftRight),yaw:(K.KeyQ?1:0)-(K.KeyE?1:0),jumpP}}
function retarget(ex){let best=-1,bd=1e9;cars.forEach((c,i)=>{if(!c.alive||i===ex)return;const d=Math.hypot(c.x-ball.p.x,c.y-ball.p.y,c.z-ball.p.z);if(d<bd){bd=d;best=i}});if(best<0)best=cars.findIndex(c=>c.alive);ball.tgt=best}
function bulletInit(){if(mode!=='bullet')return;ball.p.set(0,18,0);ball.v.set(0,0,0);ball.spd=26;ball.frz=0;ball.last=-1;ball.tgt=1+Math.floor(Math.random()*3);endT=-1}
function bulletStep(dt){
  cars.forEach((c,i)=>{if(c.alive)carStep(c,i===0?playerInp():botInput(c),dt)});
  jumpP=false;
  const A=cars.filter(c=>c.alive);
  for(let i=0;i<A.length;i++)for(let j=i+1;j<A.length;j++){const a=A[i],b=A[j],dx=b.x-a.x,dz=b.z-a.z,d=Math.hypot(dx,dz);if(d<3.4&&d>0.01&&Math.abs(a.y-b.y)<1.5){const o=(3.4-d)/2,nx=dx/d,nz=dz/d;a.x-=nx*o;a.z-=nz*o;b.x+=nx*o;b.z+=nz*o}}
  if(endT>=0){endT-=dt;if(endT<0){over('You were eliminated');return}}
  const B=ball;
  if(B.frz>0){B.frz-=dt;B.v.set(0,0,0);if(B.frz<=0)retarget(B.last)}
  else{
    const t=cars[B.tgt];B.spd=Math.min(78,B.spd+1.3*dt);
    const to=new THREE.Vector3(t.x-B.p.x,t.y-B.p.y,t.z-B.p.z).normalize();
    const cur=B.v.lengthSq()>1?B.v.clone().normalize():to.clone();
    cur.lerp(to,Math.min(1,2.4*dt)).normalize();B.v.copy(cur).multiplyScalar(B.spd);
    ballStep(dt);
  }
  const me=cars[B.tgt];
  if(B.tgt===0&&K.KeyG&&me.dodgeT>0&&B.frz<=0){let bi=-1,bd=7;cars.forEach((c,i)=>{if(i===0||!c.alive)return;const d=Math.hypot(c.x-me.x,c.y-me.y,c.z-me.z);if(d<bd){bd=d;bi=i}});if(bi>=0){B.tgt=bi;flash('Grab!')}}
  if(B.frz<=0){
    const t=cars[B.tgt],d=Math.hypot(t.x-B.p.x,t.y-B.p.y,t.z-B.p.z);
    if(d<R+2){
      const from=B.tgt;
      if(t.dodgeT>0&&t.dodgeDir>0){retarget(from);B.last=from;B.spd=Math.min(80,B.spd+4);const n=cars[B.tgt];B.v.set(n.x-B.p.x,n.y-B.p.y,n.z-B.p.z).normalize().multiplyScalar(B.spd);B.p.addScaledVector(B.v,0.06);if(from===0)flash('Parry!')}
      else if(t.dodgeT>0){B.frz=1.6;B.last=from;B.v.set(0,0,0);if(from===0)flash('Frozen')}
      else{
        boom(t.x,t.y,t.z);t.alive=false;t.m.visible=false;
        const left=cars.filter(c=>c.alive).length;
        if(from===0){endT=2.2;flash('Demolished!')}
        if(left<=1){over(cars[0].alive?'You win!':(left?'Bot wins':'Draw'));return}
        retarget(from);
      }
    }
  }
}
// ---------- rock paper scissors sudden death ----------
let otT=0,rpsBusy=false;
const RPS=['\u270a','\u270b','\u270c\ufe0f'];
function startRps(){st='rps';rpsBusy=false;$('msg').textContent='';$('rv').textContent='';$('rt').textContent='A full minute of overtime and still tied. Rock, paper, scissors decides it. Click one (or press 1, 2, 3).';$('rps').classList.remove('hid')}
function rpsPick(m){
  if(st!=='rps'||rpsBusy)return;rpsBusy=true;const b=Math.floor(Math.random()*3);
  ['Rock...','Paper...','Scissors...','Shoot!'].forEach((t,i)=>setTimeout(()=>{if(st==='rps')$('rt').textContent=t},i*450));
  setTimeout(()=>{
    if(st!=='rps')return;
    $('rv').textContent=RPS[m]+'  vs  '+RPS[b];const r=(m-b+3)%3;
    if(r===0){$('rt').textContent='Tie! Go again...';setTimeout(()=>{if(st!=='rps')return;rpsBusy=false;$('rt').textContent='Pick again.';$('rv').textContent=''},1200)}
    else{const win=r===1;$('rt').textContent=win?'You win!':'Orange wins';score[win?0:1]++;setTimeout(()=>{if(st!=='rps')return;$('rps').classList.add('hid');over(win?'Blue wins!':'Orange wins!')},1300)}
  },4*450);
}
document.querySelectorAll('#rb button').forEach(b=>b.onclick=()=>rpsPick(+b.dataset.m));
addEventListener('keydown',e=>{const k={Digit1:0,Digit2:1,Digit3:2}[e.code];if(k!==undefined)rpsPick(k)});
let last=performance.now(),acc=0;
function loop(now){
  requestAnimationFrame(loop);
  const fd=Math.min(0.05,(now-last)/1000);last=now;acc+=fd;
  while(acc>=1/60){if(st!=='menu'&&st!=='over')step(1/60);acc-=1/60}
  ball.m.visible=mode!=='bullet';bm.visible=mode==='bullet'&&!menuOpen;bm.position.copy(ball.p);bm.children[0].material.color.setHex(ball.frz>0?0x59d7ff:0xb04dff);
  const tg=mode==='bullet'&&st==='play'&&cars[ball.tgt];tm.visible=!!(tg&&tg.alive);if(tm.visible)tm.position.set(tg.x,tg.y+4.5+Math.sin(now/150)*0.5,tg.z);
  for(let i=fx.length-1;i>=0;i--){const e=fx[i];e.t+=fd;e.m.scale.setScalar(1+e.t*9);e.m.material.opacity=Math.max(0,0.9-e.t*1.6);if(e.t>0.6){sc.remove(e.m);fx.splice(i,1)}}
  ball.m.position.copy(ball.p);ball.m.rotation.x+=ball.v.z*fd/R*0.9;ball.m.rotation.z-=ball.v.x*fd/R*0.9;
  if(st==='menu'||st==='over'){ball.m.position.y=R;}
  updCam(fd);if(menuOpen)animLobby(fd,now/1000);
  $('sb').textContent=score[0];$('so').textContent=score[1];
  $('sb').style.display=$('so').style.display=mode==='bullet'?'none':'';const t=Math.ceil(timer);$('st').textContent=mode==='bullet'?cars.filter(c=>c.alive).length+' alive':ot?'OT '+Math.max(0,Math.ceil(60-otT)):Math.floor(t/60)+':'+String(t%60).padStart(2,'0');
  const b=cars[0].boost;$('bn').textContent=mode==='zerog'?'\u221e':Math.round(b);$('boost').style.setProperty('--b',b/100);
  rd.render(menuOpen?lob:sc,menuOpen?lcam:cam);
}
spawn();lobbyInit();requestAnimationFrame(loop);
</script>
</body>
</html>
