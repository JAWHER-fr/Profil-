<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="utf-8" />
  <title>💜 Love Zone - Pour Toi</title>
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <style>
    * { box-sizing: border-box; margin:0; padding:0; font-family: "Courier New", monospace; }
    body { background: radial-gradient(circle at center, #2a002a, #000); color:#ff4da6; min-height:100vh; overflow:hidden; }
    header { text-align:center; padding:22px; border-bottom:2px solid #ff4da6; background: rgba(0,0,0,0.5); }
    header h1 { font-size:2.4rem; text-shadow:0 0 15px #ff4da6; animation:pulse 2s infinite; }
    section { max-width:900px; margin:32px auto; padding:20px; }
    .card { background:rgba(0,0,0,0.6); border:1px solid #ff4da6; padding:18px; border-radius:12px; box-shadow:0 0 20px #ff4da6; white-space:pre-wrap; min-height:180px; }
    #matrix { position:fixed; inset:0; z-index:-1; background:black; }
    @keyframes pulse { 0%{text-shadow:0 0 12px #ff4da6}50%{text-shadow:0 0 28px #ff99cc}100%{text-shadow:0 0 12px #ff4da6} }

    /* Overlay quand la voix est bloquée */
    #audio-overlay {
      position:fixed; inset:0; display:none; align-items:center; justify-content:center;
      background: rgba(0,0,0,0.65); z-index:1000;
    }
    .overlay-card {
      background: #111; color:#fff; border:2px solid #ff4da6; padding:22px; border-radius:12px; text-align:center;
      box-shadow:0 0 30px #ff4da6; max-width:90%; width:420px;
    }
    .overlay-card p { margin-bottom:16px; color:#ffccdf; }
    .btn {
      background:#ff4da6; color:#111; border:none; padding:10px 16px; border-radius:8px; font-size:1rem; cursor:pointer;
      box-shadow:0 0 12px #ff99cc; margin:6px;
    }
    .linklike { background:transparent; color:#ff4da6; border:1px solid #ff4da6; padding:8px 12px; border-radius:8px; cursor:pointer; margin:6px; }
    footer { text-align:center; margin-top:18px; opacity:0.7; font-size:0.9rem; }
  </style>
</head>
<body>

<canvas id="matrix"></canvas>

<header><h1>💌 Pour Toi, Mon Amour</h1></header>

<section>
  <h2 style="text-align:center; margin-bottom:12px; text-shadow:0 0 10px #ff4da6;">💻 Terminal de mon Cœur</h2>
  <div class="card"><pre id="profile-terminal"></pre></div>
  <footer id="hint" style="opacity:0.9; margin-top:12px; text-align:center;">Le message sera lu automatiquement à la fin de l'écriture (si le navigateur le permet).</footer>
</section>

<!-- Overlay qui s'affiche si la lecture automatique est bloquée -->
<div id="audio-overlay" role="dialog" aria-modal="true">
  <div class="overlay-card">
    <h3>🔇 Lecture bloquée</h3>
    <p>Le navigateur a empêché la lecture automatique. Cliquez pour autoriser la lecture vocale.</p>
    <div>
      <button id="overlay-play" class="btn">▶️ Autoriser et écouter</button>
      <button id="overlay-retry" class="linklike">Réessayer automatiquement</button>
    </div>
    <p style="font-size:0.85rem; margin-top:12px; color:#ffd6ea;">Astuce : si ça ne marche pas, teste dans Firefox ou vérifie les paramètres son du navigateur.</p>
  </div>
</div>

<script>
/* ---------- Texte + dactylographie ---------- */
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
    // tout tapé -> tenter la lecture
    onTypingFinished();
  }
}
typeLine();

/* ---------- Gestion voix / fallback ---------- */
const overlay = document.getElementById('audio-overlay');
const overlayPlay = document.getElementById('overlay-play');
const overlayRetry = document.getElementById('overlay-retry');

let availableVoices = [];
function loadVoices() {
  availableVoices = speechSynthesis.getVoices() || [];
}
if ('speechSynthesis' in window) {
  loadVoices();
  window.speechSynthesis.onvoiceschanged = loadVoices;
}

/* Construire le texte final (sans les lignes de =====) */
function buildSpeechText() {
  return profileLines.filter(l => !/^=+$/.test(l)).join(' ');
}

/* Créer un utterance prêt à jouer */
function createUtterance(text) {
  const u = new SpeechSynthesisUtterance(text);
  u.lang = 'fr-FR';
  u.rate = 1.05;
  // choisir une voix FR si dispo
  const french = availableVoices.find(v => v.lang && v.lang.startsWith('fr'));
  if (french) u.voice = french;
  return u;
}

/* Tente de parler et renvoie une Promise qui se résout si la lecture a bien démarré */
function trySpeakOnce(timeoutMs = 900) {
  return new Promise((resolve, reject) => {
    if (!('speechSynthesis' in window)) {
      reject(new Error('SpeechSynthesis non supporté'));
      return;
    }
    const text = buildSpeechText();
    const u = createUtterance(text);
    let started = false;
    u.onstart = () => { started = true; resolve({status:'started'}); };
    u.onerror = (e) => { reject(e || new Error('Speech error')); };
    // onend: ok aussi (si start n'a pas été appelé, start est meilleur indicateur)
    u.onend = () => { if (!started) reject(new Error('ended without start')); };

    try {
      window.speechSynthesis.speak(u);
    } catch (err) {
      reject(err);
      return;
    }

    // si après timeout la lecture n'a pas démarré => on considère que c'est bloqué
    setTimeout(() => {
      if (!started) {
        // essayer d'annuler ce qu'on a demandé (pour éviter accumulation)
        try { window.speechSynthesis.cancel(); } catch(e){}
        reject(new Error('blocked-or-not-started'));
      }
    }, timeoutMs);
  });
}

/* Appelé automatiquement quand la dactylo est finie */
function onTypingFinished() {
  // essai automatique
  trySpeakOnce()
    .then(() => {
      // tout OK, la voix a démarré
      console.log('Lecture vocale démarrée automatiquement.');
      hideOverlay();
    })
    .catch((err) => {
      console.warn('Auto speak failed:', err);
      // montrer overlay pour que l'utilisateur clique
      showOverlay();
    });
}

/* Afficher / cacher overlay */
function showOverlay() {
  overlay.style.display = 'flex';
}
function hideOverlay() {
  overlay.style.display = 'none';
}

/* Quand l'utilisateur clique sur "Autoriser et écouter" */
overlayPlay.addEventListener('click', () => {
  // on déclenche la lecture (cet événement est considéré "user gesture")
  try {
    const u = createUtterance(buildSpeechText());
    u.onstart = () => { hideOverlay(); console.log('Lecture démarrée après clic'); };
    u.onerror = (e) => { console.error('Erreur vocal après clic', e); alert('Erreur vocale : ' + (e.message || e)); };
    window.speechSynthesis.speak(u);
  } catch (err) {
    console.error(err);
    alert('Impossible de lancer la synthèse vocale ici.');
  }
});

/* "Réessayer automatiquement" : tente à nouveau sans clic supplémentaire,
   utile si les voix mettent du temps à charger (on garde la même logique) */
overlayRetry.addEventListener('click', () => {
  overlayRetry.disabled = true;
  overlayRetry.textContent = 'Réessai en cours...';
  trySpeakOnce(1200)
    .then(() => { hideOverlay(); })
    .catch(() => {
      overlayRetry.disabled = false;
      overlayRetry.textContent = 'Réessayer automatiquement';
      alert('Toujours bloqué — clique sur "Autoriser et écouter" pour forcer via une interaction.');
    });
});

/* ---------- Matrix animation cœurs (inchangé) ---------- */
const canvas = document.getElementById('matrix');
const ctx = canvas.getContext('2d');
function resizeCanvas(){ canvas.width = innerWidth; canvas.height = innerHeight; cols = Math.floor(canvas.width / font_size); drops.length = cols; for(let i=0;i<cols;i++) if(!drops[i]) drops[i]=1; }
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
