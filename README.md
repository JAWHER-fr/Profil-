<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Hacker Zone - Jawher Dridi</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Courier New', monospace;
    }

    body {
      background: #000;
      color: #00ff00;
    }

    header {
      background: #111;
      padding: 20px;
      text-align: center;
      border-bottom: 2px solid #00ff00;
    }

    header h1 {
      font-size: 2rem;
      text-shadow: 0 0 10px #00ff00;
    }

    section {
      padding: 40px 20px;
      max-width: 900px;
      margin: auto;
    }

    h2 {
      text-align: center;
      margin-bottom: 20px;
      text-shadow: 0 0 10px #00ff00;
      font-size: 1.5rem;
    }

    .card {
      display: flex;
      flex-direction: row;
      align-items: flex-start;
      gap: 20px;
      background: #111;
      border: 1px solid #00ff00;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 15px #00ff00;
      flex-wrap: wrap; /* permet l'empilement sur mobile */
    }

    .profile-img {
      width: 150px;
      height: 150px;
      object-fit: cover;
      border-radius: 50%;
      border: 3px solid #00ff00;
      box-shadow: 0 0 15px #00ff00;
    }

    .terminal {
      flex: 1;
      white-space: pre-wrap;
      max-height: 300px;
      overflow-y: auto;
    }

    #start-btn {
      display: block;
      margin: 20px auto;
      padding: 12px 25px;
      background: #00ff00;
      color: #000;
      border: none;
      border-radius: 8px;
      font-size: 1rem;
      font-weight: bold;
      cursor: pointer;
      box-shadow: 0 0 10px #00ff00;
      transition: 0.2s;
    }

    #start-btn:hover {
      background: #00cc00;
      box-shadow: 0 0 20px #00ff00;
    }

    /* Matrix background */
    #matrix {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: -1;
      background: black;
    }
  </style>
</head>
<body>

<canvas id="matrix"></canvas>

<header>
  <h1>Jawher Dridi</h1>
</header>

<section id="profile">
  <h2>Profil Terminal</h2>
  <button id="start-btn">▶ Activer le terminal</button>
  <div class="card">
    <img src="1756550933593.jpg" alt="Photo de test" class="profile-img">
    <pre id="profile-terminal" class="terminal"></pre>
  </div>
</section>

<script>
  /***********************
   * MATRIX BACKGROUND
   ***********************/
  const canvas = document.getElementById("matrix");
  const ctx = canvas.getContext("2d");
  canvas.height = window.innerHeight;
  canvas.width = window.innerWidth;

  const letters = "アカサタナハマヤラワ0123456789";
  const fontSize = 16;
  let columns = Math.floor(canvas.width / fontSize);
  let drops = Array(columns).fill(1);

  function drawMatrix() {
    ctx.fillStyle = "rgba(0,0,0,0.05)";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    ctx.font = fontSize + "px monospace";

    for (let i = 0; i < drops.length; i++) {
      const text = letters.charAt(Math.floor(Math.random() * letters.length));
      ctx.fillStyle = "#0F0";
      ctx.fillText(text, i * fontSize, drops[i] * fontSize);
      if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) drops[i] = 0;
      drops[i]++;
    }
  }
  setInterval(drawMatrix, 35);

  window.addEventListener('resize', () => {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    columns = Math.floor(canvas.width / fontSize);
    drops = Array(columns).fill(1);
  });

  /***********************
   * TERMINAL + VOIX
   ***********************/
  const terminal = document.getElementById("profile-terminal");
  const startBtn = document.getElementById("start-btn");

  const profileLines = [
    "===============================",
    "📂 Accessing user profile...",
    "Name: Jawher Dridi",
    "Status: Student at ISI Kef",
    "Email: dridijawherr@gmail.com",
    "Interests: Web Development, Cybersecurity, Creativity",
    "===============================",
    "✅ Profile Loaded Successfully"
  ];

  let line = 0;
  let char = 0;

  function speak(text) {
    if ('speechSynthesis' in window) {
      const utterance = new SpeechSynthesisUtterance(text);
      utterance.lang = "fr-FR";
      utterance.rate = 1.1;
      const voices = speechSynthesis.getVoices();
      if (voices.length > 0) {
        utterance.voice = voices.find(v => v.lang === "fr-FR") || voices[0];
      }
      window.speechSynthesis.speak(utterance);
    }
  }

  function typeLine() {
    if (line < profileLines.length) {
      if (char < profileLines[line].length) {
        terminal.textContent += profileLines[line][char];
        char++;
        setTimeout(typeLine, 40);
      } else {
        terminal.textContent += "\n";
        if (!/^=+$/.test(profileLines[line])) {
          speak(profileLines[line]);
        }
        line++;
        char = 0;
        setTimeout(typeLine, 300);
      }
    }
  }

  startBtn.addEventListener("click", () => {
    startBtn.style.display = "none";
    typeLine();
  });

  window.speechSynthesis.onvoiceschanged = () => {
    console.log("Voices loaded:", speechSynthesis.getVoices());
  };
</script>

</body>
</html>
