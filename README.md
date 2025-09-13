<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="utf-8" />
  <title>💜 Love Zone - Pour Toi</title>
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <style>
    * { box-sizing: border-box; margin:0; padding:0; font-family:"Courier New", monospace; }
    body { background: radial-gradient(circle at center, #2a002a, #000); color:#ff4da6; min-height:100vh; overflow:hidden; }
    header { text-align:center; padding:22px; border-bottom:2px solid #ff4da6; background: rgba(0,0,0,0.5); }
    header h1 { font-size:2.4rem; text-shadow:0 0 15px #ff4da6; animation:pulse 2s infinite; }
    section { max-width:900px; margin:32px auto; padding:20px; }
    .card { background:rgba(0,0,0,0.6); border:1px solid #ff4da6; padding:18px; border-radius:12px; box-shadow:0 0 20px #ff4da6; white-space:pre-wrap; min-height:180px; }
    #matrix { position:fixed; inset:0; z-index:-1; background:black; }
    @keyframes pulse { 0%{text-shadow:0 0 12px #ff4da6}50%{text-shadow:0 0 28px #ff99cc}100%{text-shadow:0 0 12px #ff4da6} }
    .btn {
      background:#ff4da6; color:#111; border:none; padding:10px 16px; border-radius:8px;
      font-size:1rem; cursor:pointer; box-shadow:0 0 12px #ff99cc; margin-top:20px; margin-right:10px;
    }
    #voice-permission {
      position:fixed; inset:0; display:flex; justify-content:center; align-items:center;
      background:rgba(0,0,0,0.85); z-index:5000; flex-direction:column;
    }
    #video-overlay {
      display:none; position:fixed; inset:0; background:rgba(0,0,0,0.85);
      justify-content:center; align-items:center; z-index:3000;
    }
    #video-overlay div { position:relative; max-width:90%; width:700px; }
    #close-video { position:absolute; top:-40px; right:-10px; font-size:2rem; cursor:pointer; color:#ff4da6; }
  </style>
</head>
<body>

<canvas id="matrix"></canvas>

<header><h1>💌 Pour Toi, Mon Amour</h1></header>

<section>
  <h2 style="text-align:center; margin-bottom:12px; text-shadow:0 0 10px #ff4da6;">💻 Terminal de mon Cœur</h2>
  <div class="card"><pre id="profile-terminal"></pre></div>

  <div style="text-align:center;">
    <button id="video-btn" class="btn">🎬 Voir la vidéo</button>
  </div>
</section>

<div id="video-overlay">
  <div>
    <span id="close-video">✖</span>
    <video id="love-video" width="100%" controls>
      <source src="eff382e089ea5d50f7e0772d4e14d7b3_1757729299093.mp4" type="video/mp4">
      Ton navigateur ne supporte pas la vidéo.
    </video>
  </div>
</div>

<!-- Fenêtre pour demander autorisation vocale -->
<div id="voice-permission">
  <h2>🔊 Activer la lecture automatique du message</h2>
  <button id="allow-voice" class="btn">Cliquer pour autoriser</button>
</div>

<script>
/* ---------- Variables ---------- */
let userAllowed = false;
const terminal = document.getElementById('profile-terminal');
const profileLines = [
  "===============================",
  "📂 Initialisation du cœur...",
  "from: Jawher",
  "to: Ikhlass ❤️",
  "Message : Depuis que je t’ai rencontrée,",
  "           chaque jour est une lumière dans ma vie.",
  "           Je t’aime plus que les mots ne peuvent le dire.",
  "===============================",
  "💖 Forever & Always 💖"
];

let line = 0, ch = 0;

/* ---------- Autorisation vocale via bouton ---------- */
const voiceOverlay = document.getElementById('voice-permission');
const allowVoiceBtn = document.getElementById('allow-voice');
allowVoiceBtn.addEventListener('click', () => {
  userAllowed = true;
  voiceOverlay.style.display = 'none';
  typeLine(); // Commence la dactylographie
});

/* ---------- Dactylographie ---------- */
function typeLine() {
  if (line < profileLines.length) {
    if (ch < profileLines[line].length) {
      terminal.textContent += profileLines[line][ch++];
      setTimeout(typeLine, 40);
    } else {
      terminal.textContent += '\n';
      line++; ch = 0;
      setTimeout(typeLine, 300);
    }
  } else {
    if(userAllowed) playVoice();
  }
}

/* ---------- Lecture vocale ---------- */
function getSpeechText() {
  return profileLines.filter(l => !/^=+$/.test(l)).join(" ");
}
function playVoice() {
  if('speechSynthesis' in window){
    const utterance = new SpeechSynthesisUtterance(getSpeechText());
    utterance.lang = "fr-FR";
    utterance.rate = 1.05;
    speechSynthesis.speak(utterance);
  }
}

/* ---------- Bouton vidéo complète ---------- */
const videoBtn = document.getElementById("video-btn");
const videoOverlay = document.getElementById("video-overlay");
const closeVideo = document.getElementById("close-video");
const video = document.getElementById("love-video");

videoBtn.addEventListener("click", () => {
  videoOverlay.style.display = "flex";
  video.play();
});
closeVideo.addEventListener("click", () => {
  video.pause();
  videoOverlay.style.display = "none";
});

/* ---------- Matrix animation cœurs ---------- */
const canvas = document.getElementById('matrix');
const ctx = canvas.getContext('2d');
function resizeCanvas(){ 
  canvas.width = innerWidth; 
  canvas.height = innerHeight; 
  cols = Math.floor(canvas.width / font_size); 
  drops.length = cols; 
  for(let i=0;i<cols;i++) if(!drops[i]) drops[i]=1; 
}
const hearts = "💜💖💕💘💝💞💟";
const font_size = 18;
let cols = Math.floor(window.innerWidth / font_size);
const drops = Array.from({length:cols},()=>1);
resizeCanvas();
window.addEventListener('resize', resizeCanvas);
function draw() {
  ctx.fillStyle = "rgba(0,0,0,0.05)";
  ctx.fillRect(0,0,canvas.width,canvas.height);
  ctx.fillStyle = "#ff4da6";
  ctx.font = font_size + "px Courier New";
  for (let i=0;i<drops.length;i++){
    const text = hearts[Math.floor(Math.random()*hearts.length)];
    ctx.fillText(text, i*font_size, drops[i]*font_size);
    if (drops[i]*font_size > canvas.height && Math.random() > 0.975) drops[i]=0;
    drops[i]++;
  }
}
setInterval(draw, 80);
</script>

</body>
</html>
