<iframe src="live-mutual-calculator.html" width="440" height="520" style="border:none; border-radius:12px;"></iframe>


<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Live Seller Qualification Calculator</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f9fafb;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
    .calculator {
      background: white;
      padding: 2rem;
      border-radius: 1rem;
      box-shadow: 0 4px 15px rgba(0,0,0,0.1);
      max-width: 420px;
      text-align: center;
    }
    h2 {
      color: #2563eb;
      margin-bottom: 1.5rem;
    }
    select, input {
      width: 100%;
      padding: 0.6rem;
      border-radius: 0.5rem;
      border: 1px solid #d1d5db;
      margin-bottom: 1rem;
      font-size: 1rem;
    }
    button {
      width: 100%;
      padding: 0.75rem;
      background: #2563eb;
      color: white;
      border: none;
      border-radius: 0.5rem;
      font-size: 1.1rem;
      cursor: pointer;
    }
    button:hover {
      background: #1e40af;
    }
    .result {
      font-size: 1.4rem;
      color: #111827;
      font-weight: bold;
      margin-top: 1.5rem;
    }
    .breakdown {
      margin-top: 0.75rem;
      font-size: 1rem;
      color: #374151;
    }
  </style>
</head>
<body>
  <div class="calculator">
    <h2>Seller Qualification Calculator</h2>

    <label for="mutualSelect">Select Mutual:</label>
    <select id="mutualSelect">
      <option value="">Loading options...</option>
    </select>

    <label for="priceInput">Enter Price Point ($350,000 → 350):</label>
    <input type="number" id="priceInput" placeholder="e.g. 350" />

    <button onclick="calculate()">Calculate</button>

    <div class="result" id="result">Required Monthly Income: $0.00</div>
    <div class="breakdown" id="breakdown"></div>
  </div>

  <script>
    const SHEET_URL =
      "https://docs.google.com/spreadsheets/d/e/2PACX-1vTxj9qLBMXYW4tANhidLLkpV_ZyERFuKRUlMYlNR1BKotVZbMw-iDq_cpL7C_40AmEOhQdGaNMP8RLV/pub?gid=0&single=true&output=csv";

    let data = {};

    async function loadData() {
      try {
        const response = await fetch(SHEET_URL);
        const text = await response.text();
        const rows = text.trim().split("\n").map(r => r.split(","));

        // Extract headers
        const headers = rows[0].map(h => h.trim().toUpperCase());
        const mutualIndex = headers.indexOf("MUTUAL");
        const monthlyIndex = headers.indexOf("MONTHLY");
        const ocIndex = headers.indexOf("OC USER FEES");
        const reqIndex = headers.indexOf("MONTHLY REQUIREMENT");

        // Parse rows
        for (let i = 1; i < rows.length; i++) {
          const row = rows[i];
          const mutual = row[mutualIndex]?.trim();
          const monthly = parseFloat(row[monthlyIndex]?.replace(/[^0-9.]/g, "")) || 0;
          const oc = parseFloat(row[ocIndex]?.replace(/[^0-9.]/g, "")) || 0;
          const requirement = row[reqIndex] || "";
          const multiplierMatch = requirement.match(/(\d+)/);
          const multiplier = multiplierMatch ? parseInt(multiplierMatch[1]) : 1;

          if (mutual) {
            data[mutual] = { monthly, oc, multiplier };
          }
        }

        populateDropdown();
      } catch (error) {
        console.error("Error loading sheet:", error);
        alert("Unable to load data from Google Sheet. Please check permissions.");
      }
    }

    function populateDropdown() {
      const select = document.getElementById("mutualSelect");
      select.innerHTML = '<option value="">-- Select --</option>';
      for (const key in data) {
        const item = data[key];
        select.innerHTML += `<option value="${key}">${key} ($${item.monthly} + $${item.oc}, ${item.multiplier}x)</option>`;
      }
    }

    function calculate() {
      const mutual = document.getElementById("mutualSelect").value;
      const price = parseFloat(document.getElementById("priceInput").value);
      const resultEl = document.getElementById("result");
      const breakdownEl = document.getElementById("breakdown");

      if (!mutual || isNaN(price)) {
        resultEl.textContent = "Please select a Mutual and enter a valid price.";
        breakdownEl.innerHTML = "";
        return;
      }

      const { monthly, oc, multiplier } = data[mutual];
      const total = (monthly + oc + price) * multiplier;

      resultEl.textContent = `Required Monthly Income: $${total.toFixed(2)}`;
      breakdownEl.innerHTML = `
        Base Monthly Fee: $${monthly.toFixed(2)}<br>
        OC User Fee: $${oc.toFixed(2)}<br>
        Price Point: $${price.toFixed(2)}<br>
        Multiplier: ${multiplier}x<br>
        <strong>Calculation:</strong> (${monthly.toFixed(2)} + ${oc.toFixed(2)} + ${price.toFixed(2)}) × ${multiplier} = $${total.toFixed(2)}
      `;
    }

    loadData();

    // Optional: auto-refresh data every 10 minutes
    setInterval(loadData, 600000);
  </script>
</body>
</html>
