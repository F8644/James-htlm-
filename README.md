<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Simple Calculator</title>
  <style>
    * {
      box-sizing: border-box;
    }

    body {
      display: grid;
      min-height: 100vh;
      margin: 0;
      place-items: center;
      background: #f1f5f9;
      font-family: system-ui, sans-serif;
    }

    .calculator {
      width: min(320px, calc(100vw - 32px));
      padding: 20px;
      border-radius: 16px;
      background: #1e293b;
      box-shadow: 0 12px 30px #0f172a33;
    }

    #display {
      width: 100%;
      height: 72px;
      margin-bottom: 16px;
      padding: 12px;
      border: 0;
      border-radius: 10px;
      background: #f8fafc;
      color: #0f172a;
      font-size: 2rem;
      text-align: right;
    }

    .buttons {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
    }

    button {
      min-height: 56px;
      border: 0;
      border-radius: 10px;
      background: #334155;
      color: white;
      font-size: 1.2rem;
      cursor: pointer;
    }

    button:hover {
      background: #475569;
    }

    button.operator {
      background: #2563eb;
    }

    button.operator:hover {
      background: #1d4ed8;
    }

    button.clear {
      background: #dc2626;
    }

    button.equals {
      grid-column: span 2;
      background: #16a34a;
    }
  </style>
</head>
<body>
  <main class="calculator" aria-label="Calculator">
    <input id="display" type="text" value="0" aria-label="Display" readonly />
    <div class="buttons">
      <button class="clear" data-action="clear">C</button>
      <button data-action="backspace">⌫</button>
      <button class="operator" data-operation="/">÷</button>
      <button class="operator" data-operation="*">×</button>

      <button data-number="7">7</button>
      <button data-number="8">8</button>
      <button data-number="9">9</button>
      <button class="operator" data-operation="-">−</button>

      <button data-number="4">4</button>
      <button data-number="5">5</button>
      <button data-number="6">6</button>
      <button class="operator" data-operation="+">+</button>

      <button data-number="1">1</button>
      <button data-number="2">2</button>
      <button data-number="3">3</button>
      <button data-number="0">0</button>

      <button data-action="decimal">.</button>
      <button class="equals" data-action="equals">=</button>
    </div>
  </main>

  <script>
    const display = document.querySelector("#display");
    const buttons = document.querySelector(".buttons");

    let current = "0";
    let storedValue = null;
    let operation = null;
    let startNewNumber = false;

    function updateDisplay() {
      display.value = current;
    }

    function enterNumber(number) {
      if (startNewNumber || current === "Error") {
        current = number;
        startNewNumber = false;
      } else {
        current = current === "0" ? number : current + number;
      }
      updateDisplay();
    }

    function chooseOperation(nextOperation) {
      if (current === "Error") return;

      if (operation && !startNewNumber) calculate();

      storedValue = Number(current);
      operation = nextOperation;
      startNewNumber = true;
    }

    function calculate() {
      if (operation === null || storedValue === null) return;

      const a = storedValue;
      const b = Number(current);
      let result;

      switch (operation) {
        case "+": result = a + b; break;
        case "-": result = a - b; break;
        case "*": result = a * b; break;
        case "/": result = b === 0 ? NaN : a / b; break;
      }

      current = Number.isFinite(result)
        ? String(Number(result.toPrecision(12)))
        : "Error";

      storedValue = null;
      operation = null;
      startNewNumber = true;
      updateDisplay();
    }

    buttons.addEventListener("click", (event) => {
      const button = event.target.closest("button");
      if (!button) return;

      if (button.dataset.number !== undefined) {
        enterNumber(button.dataset.number);
      } else if (button.dataset.operation) {
        chooseOperation(button.dataset.operation);
      } else {
        switch (button.dataset.action) {
          case "clear":
            current = "0";
            storedValue = null;
            operation = null;
            startNewNumber = false;
            updateDisplay();
            break;
          case "backspace":
            if (!startNewNumber && current !== "Error") {
              current = current.length > 1 ? current.slice(0, -1) : "0";
              updateDisplay();
            }
            break;
          case "decimal":
            if (startNewNumber || current === "Error") {
              current = "0.";
              startNewNumber = false;
            } else if (!current.includes(".")) {
              current += ".";
            }
            updateDisplay();
            break;
          case "equals":
            calculate();
            break;
        }
      }
    });
  </script>
</body>
</html>
