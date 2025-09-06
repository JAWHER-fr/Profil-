<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
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
      overflow: hidden;
    }
    header {
      background: #111;
      padding: 20px;
      text-align: center;
      border-bottom: 2px solid #00ff00;
    }
    header h1 {
      font-size: 2.5rem;
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
    }
    .card {
      background: #111;
      border: 1px solid #00ff00;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 15px #00ff00;
      white-space: pre-wrap;
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
  <h1>Hacker Zone - Jawher Dridi</h1>
</header>

<section id="profile">
  <h2>Profil Terminal</h2>
  <div class="card">
    <pre id="profile-terminal"></pre>
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

  const letters = "01";
  const fontSize = 16;
  const columns = canvas.width / fontSize;
  const drops = [];

  for (let x = 0; x < columns; x++) {
    drops[x] = 1;
  }

  function draw() {
    ctx.fillStyle = "rgba(0,0,0,0.05)";
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    ctx.fillStyle = "#0F0";
    ctx.font = fontSize + "px monospace";

    for (let i = 0; i < drops.length; i++) {
      const text = letters.charAt(Math.floor(Math.random() * letters.length));
      ctx.fillText(text, i * fontSize, drops[i] * fontSize);

      if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) {
        drops[i] = 0;
      }
      drops[i]++;
    }
  }

  setInterval(draw, 35);

  /***********************
   * TERMINAL + VOICE
   ***********************/
  const terminal = document.getElementById("profile-terminal");

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
      utterance.lang = "fr-FR"; // voix française
      utterance.rate = 1.1; // vitesse
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
        // Parler chaque ligne
        speak(profileLines[line]);
        line++;
        char = 0;
        setTimeout(typeLine, 300);
      }
    }
  }

  typeLine();
</script>

</body>
</html>
