<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>MiniCraft Web</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
html,body { margin:0; padding:0; overflow:hidden; background:#000; }
#ui {
  position:fixed; top:10px; left:10px;
  color:white; font-family:Arial;
  background:rgba(0,0,0,.6);
  padding:8px; border-radius:6px;
}
#inv {
  position:fixed; bottom:10px; left:50%;
  transform:translateX(-50%);
  display:flex;
}
.slot {
  width:40px; height:40px;
  background:#222; border:2px solid #555;
  margin:2px; color:white;
  display:flex; align-items:center; justify-content:center;
}
.slot.active { border-color:yellow; }
#jump {
  position:fixed; right:20px; bottom:80px;
  width:60px; height:60px;
  border-radius:50%;
  background:rgba(255,255,255,.2);
  color:white; font-size:20px;
}
</style>
</head>

<body>

<div id="ui">
❤️ Vida: <span id="hp">20</span><br>
Modo: <span id="mode">survival</span>
</div>

<div id="inv"></div>
<button id="jump">↑</button>

<script src="https://cdn.jsdelivr.net/npm/three@0.152/build/three.min.js"></script>

<script>
/* ===== ESCENA ===== */
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x87ceeb);

const camera = new THREE.PerspectiveCamera(75, innerWidth/innerHeight, 0.1, 1000);
camera.position.set(0,2,5);

const renderer = new THREE.WebGLRenderer({antialias:true});
renderer.setSize(innerWidth, innerHeight);
document.body.appendChild(renderer.domElement);

window.onresize=()=>{
  camera.aspect=innerWidth/innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(innerWidth,innerHeight);
};

/* ===== LUCES ===== */
const sun = new THREE.DirectionalLight(0xffffff,1);
sun.position.set(10,20,10);
scene.add(sun);
scene.add(new THREE.AmbientLight(0x404040));

/* ===== JUEGO ===== */
let hp=20, mode="survival";
let time=0;
const blocks=[];
const mobs=[];

/* ===== TEXTURAS ===== */
const loader=new THREE.TextureLoader();
const grass=new THREE.MeshLambertMaterial({map:loader.load("https://i.imgur.com/8Kp1QYj.png")});
const dirt=new THREE.MeshLambertMaterial({map:loader.load("https://i.imgur.com/RxYxYqJ.png")});

/* ===== BLOQUES ===== */
function makeBlock(x,y,z,mat){
  const b=new THREE.Mesh(new THREE.BoxGeometry(1,1,1),mat);
  b.position.set(x,y,z);
  scene.add(b);
  blocks.push(b);
}

for(let x=-10;x<10;x++)
for(let z=-10;z<10;z++){
  makeBlock(x,0,z,grass);
  makeBlock(x,-1,z,dirt);
}

/* ===== INVENTARIO ===== */
const inv=new Array(9).
