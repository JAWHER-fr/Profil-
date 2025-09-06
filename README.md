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
    #start-btn {
      display: block;
      margin: 30px auto;
      padding: 12px 25px;
      background: #00ff00;
      color: #000;
      border: none;
      border-radius: 8px;
      font-size: 1.1rem;
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
  <h1>Hacker Zone - Jawher Dridi</h1>
</header>

<section id="profile">
  <h2>Profil Terminal</h2>
  <button id="start-btn">▶ Activer le terminal</button>
  <div class="card">
    <pre id="profile-terminal"></pre>
  </div>
</section>

<script>
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

  // Fonction voix
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

  // Fonction écriture ligne par ligne
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

  // Attente clic utilisateur pour activer voix + terminal
  startBtn.addEventListener("click", () => {
    startBtn.style.display = "none"; // cacher bouton
    typeLine();
  });

  // Chargement des voix disponibles
  window.speechSynthesis.onvoiceschanged = () => {
    console.log("Voices loaded:", speechSynthesis.getVoices());
  };
</script>
</body>
</html>
