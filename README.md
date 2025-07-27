# Wheel-of-Rap
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Wheel of Rap Beef</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      background-color: #111;
      color: white;
    }
    h1 {
      color: gold;
    }
    .board {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      margin: 20px;
    }
    .tile {
      width: 40px;
      height: 50px;
      border: 2px solid #555;
      margin: 4px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 24px;
      background-color: #222;
    }
    .controls {
      margin: 20px;
    }
    .wheel-result {
      font-size: 24px;
      color: lightgreen;
      margin: 10px 0;
    }
    button {
      padding: 10px 20px;
      margin: 5px;
      font-size: 16px;
      cursor: pointer;
    }
    input {
      font-size: 16px;
      padding: 5px;
      width: 200px;
    }
    .money {
      font-size: 20px;
      color: yellow;
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <h1>Wheel of Rap Beef 🎤</h1>
  <p><strong>Clue:</strong> Rap Beef</p>
  <div class="board" id="board"></div>
  <div class="controls">
    <button onclick="spinWheel()">🎡 Spin Wheel</button>
    <div class="wheel-result" id="wheelResult"></div>

    <input type="text" id="guessInput" maxlength="1" placeholder="Enter letter" />
    <button onclick="guessLetter()">Guess Letter</button>
    <br>
    <button onclick="buyVowel()">Buy Vowel ($250)</button>
    <br><br>
    <input type="text" id="solveInput" placeholder="Solve the puzzle" />
    <button onclick="solvePuzzle()">Submit Solution</button>
    <div class="money">Money: $<span id="money">0</span></div>
  </div>

  <script>
    const answer = "SAY DRAKE I HEAR YOU LIKE THEM YOUNG".toUpperCase();
    const clue = "Rap Beef";
    const vowels = ["A", "E", "I", "O", "U"];
    let guessed = new Set();
    let money = 0;
    let currentSpin = 0;
    let isSpinning = false;

    const boardEl = document.getElementById("board");
    const wheelResultEl = document.getElementById("wheelResult");
    const moneyEl = document.getElementById("money");

    const wheelValues = [100, 200, 300, 400, 500, 600, "BANKRUPT", "LOSE A TURN"];

    function renderBoard() {
      boardEl.innerHTML = "";
      for (const char of answer) {
        const tile = document.createElement("div");
        tile.className = "tile";
        if (char === " ") {
          tile.style.border = "none";
        } else if (guessed.has(char)) {
          tile.textContent = char;
        } else {
          tile.textContent = "_";
        }
        boardEl.appendChild(tile);
      }
    }

    function spinWheel() {
      if (isSpinning) return;
      isSpinning = true;
      const result = wheelValues[Math.floor(Math.random() * wheelValues.length)];
      currentSpin = result;
      wheelResultEl.textContent = `Wheel: ${result}`;
      isSpinning = false;
    }

    function guessLetter() {
      const input = document.getElementById("guessInput");
      const letter = input.value.toUpperCase();
      input.value = "";

      if (!letter || letter.length !== 1 || vowels.includes(letter) || guessed.has(letter)) {
        alert("Enter a valid, unused consonant.");
        return;
      }

      if (typeof currentSpin !== "number") {
        if (currentSpin === "BANKRUPT") {
          money = 0;
          updateMoney();
          alert("BANKRUPT! You lost all your money.");
        } else if (currentSpin === "LOSE A TURN") {
          alert("Oops! Lose a turn.");
        }
        return;
      }

      guessed.add(letter);
      const count = [...answer].filter(c => c === letter).length;

      if (count > 0) {
        money += currentSpin * count;
        updateMoney();
      } else {
        alert("Incorrect guess.");
      }

      renderBoard();
      checkWin();
    }

    function buyVowel() {
      const input = document.getElementById("guessInput");
      const letter = input.value.toUpperCase();
      input.value = "";

      if (!vowels.includes(letter) || guessed.has(letter)) {
        alert("Enter a valid, unused vowel.");
        return;
      }

      if (money < 250) {
        alert("Not enough money to buy a vowel.");
        return;
      }

      guessed.add(letter);
      money -= 250;
      updateMoney();
      renderBoard();
      checkWin();
    }

    function solvePuzzle() {
      const solveInput = document.getElementById("solveInput");
      const guess = solveInput.value.toUpperCase();
      if (guess === answer) {
        for (const char of answer) guessed.add(char);
        renderBoard();
        alert("🎉 Correct! You solved the puzzle!");
      } else {
        alert("Incorrect solution. Try again.");
      }
    }

    function updateMoney() {
      moneyEl.textContent = money;
    }

    function checkWin() {
      if ([...answer].every(c => c === " " || guessed.has(c))) {
        alert("🎉 Puzzle Complete!");
      }
    }

    renderBoard();
  </script>
</body>
</html>
