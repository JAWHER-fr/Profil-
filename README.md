<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>💜 Love Zone - Pour Toi</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Courier New', monospace;
    }
    body {
      background: radial-gradient(circle at center, #2a002a, #000);
      color: #ff4da6;
      overflow: hidden;
    }
    header {
      background: rgba(0, 0, 0, 0.7);
      padding: 20px;
      text-align: center;
      border-bottom: 2px solid #ff4da6;
    }
    header h1 {
      font-size: 2.5rem;
      text-shadow: 0 0 15px #ff4da6, 0 0 30px #ff99cc;
      animation: pulse 2s infinite;
    }
    section {
      padding: 40px 20px;
      max-width: 900px;
      margin: auto;
    }
    h2 {
      text-align: center;
      margin-bottom: 20px;
      text-shadow: 0 0 10px #ff4da6;
    }
    .card {
      background: rgba(0, 0, 0, 0.6);
      border: 1px solid #ff4da6;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 0 20px #ff4da6, 0 0 40px #ff99cc;
      white-space: pre-wrap;
    }
    /* Matrix background love edition */
    #matrix {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: -1;
      background: black;
    }
    @keyframes pulse {
      0% { text-shadow: 0 0 10px #ff4da6, 0 0 30px #ff99cc; }
      50% { text-shadow: 0 0 20px #ff99cc, 0 0 40px #ff4da6; }
      100% { text-shadow: 0 0 10px #ff4da6, 0 0 30px #ff99cc; }
    }
  </style>
</head>
<body>

<canvas id="matrix"></canvas>

<header>
  <h1>💌 Pour Toi, Mon Amour</h1>
</header>

<section id="profile">
  <h2>💻 Terminal de mon Cœur</h2>
  <div class="card">
    <pre id="profile-terminal"></pre>
  </div>
</section>
<script>
  const terminal = document.getElementById("profile-terminal");

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

  let line = 0;
  let char = 0;

  function speak(text) {
    if ('speechSynthesis' in window) {
      const utterance = new SpeechSynthesisUtterance(text);
      utterance.lang = "fr-FR"; 
      utterance.rate = 1.05;
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

  typeLine();

  // Matrix style coeur 💜
  const canvas = document.getElementById("matrix");
  const ctx = canvas.getContext("2d");

  canvas.height = window.innerHeight;
  canvas.width = window.innerWidth;

  const hearts = "💜💖💕💘💝💞💟";
  const font_size = 18;
  const columns = canvas.width / font_size;
  const drops = [];

  for (let x = 0; x < columns; x++) drops[x] = 1;

  function draw() {
    ctx.fillStyle = "rgba(0, 0, 0, 0.05)";
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    ctx.fillStyle = "#ff4da6";
    ctx.font = font_size + "px Courier New";

    for (let i = 0; i < drops.length; i++) {
      const text = hearts[Math.floor(Math.random() * hearts.length)];
      ctx.fillText(text, i * font_size, drops[i] * font_size);

      if (drops[i] * font_size > canvas.height && Math.random() > 0.975)
        drops[i] = 0;

      drops[i]++;
    }
  }

  setInterval(draw, 80);
</script>

</body>
</html>
