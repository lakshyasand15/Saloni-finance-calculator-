
<!DOCTYPE html>
<html>
<head>
    <title>Saloni Finance Pro Calculator</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #0f172a; padding: 20px; display: flex; justify-content: center; color: #333; }
        .calc-card { background: white; padding: 30px; border-radius: 16px; box-shadow: 0 10px 25px rgba(0,0,0,0.3); width: 400px; }
        h2 { color: #1e293b; text-align: center; margin-top: 0; border-bottom: 2px solid #e2e8f0; padding-bottom: 10px; }
        label { display: block; margin: 15px 0 5px; font-weight: 600; font-size: 14px; color: #475569; }
        .date-row { display: flex; gap: 5px; margin-bottom: 10px; }
        input, select { width: 100%; padding: 10px; border: 2px solid #e2e8f0; border-radius: 8px; box-sizing: border-box; font-size: 14px; }
        select { cursor: pointer; background: #fdfdfd; }
        .rule-info { font-size: 11px; color: #64748b; margin-top: 5px; font-style: italic; }
        button { width: 100%; padding: 14px; background: #2563eb; color: white; border: none; border-radius: 8px; cursor: pointer; font-size: 16px; font-weight: bold; margin-top: 20px; }
        button:hover { background: #1d4ed8; }
        .result { margin-top: 25px; padding: 20px; border-radius: 10px; background: #f8fafc; border: 1px solid #e2e8f0; display: none; }
        .status-tag { display: inline-block; padding: 4px 10px; border-radius: 20px; font-size: 12px; margin-bottom: 10px; font-weight: bold; }
        .late { background: #fee2e2; color: #991b1b; }
        .good { background: #dcfce7; color: #166534; }
    </style>
</head>
<body>

<div class="calc-card">
    <h2>Saloni Finance</h2>
    
    <label>Principal Amount (₹)</label>
    <input type="number" id="principal" placeholder="Enter Amount">

    <label>Start Date (Loan Given)</label>
    <div class="date-row">
        <select id="startDay"></select>
        <select id="startMonth"></select>
        <select id="startYear"></select>
    </div>

    <label>End Date (Settlement Today)</label>
    <div class="date-row">
        <select id="endDay"></select>
        <select id="endMonth"></select>
        <select id="endYear"></select>
    </div>

    <label>Monthly Interest Rate (%)</label>
    <input type="number" id="rate" value="2">

    <p class="rule-info">Rule: 4+ Days = 0.5 Month extra. <br>13+ Months = Compound Interest penalty.</p>

    <button onclick="calculate()">Calculate Settlement</button>

    <div id="resultBox" class="result">
        <div id="statusTag" class="status-tag"></div>
        <div style="font-size: 13px; color: #64748b;">Time: <span id="timeText" style="font-weight:bold; color:#1e293b;"></span></div>
        <hr style="border:0; border-top:1px solid #e2e8f0; margin:10px 0;">
        <div style="font-size: 13px; color: #64748b;">Interest: <span id="interestAmt" style="font-weight:bold; color:#1e293b;"></span></div>
        <div style="font-size: 13px; color: #64748b; margin-top:5px;">Total Payable:</div>
        <div id="totalAmt" style="font-size: 26px; font-weight: 800; color: #2563eb;"></div>
    </div>
</div>

<script>
// Fill the Dropdowns automatically
function fillDropdowns() {
    const days = document.querySelectorAll('#startDay, #endDay');
    const months = document.querySelectorAll('#startMonth, #endMonth');
    const years = document.querySelectorAll('#startYear, #endYear');
    const monthNames = ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"];

    days.forEach(d => {
        for(let i=1; i<=31; i++) {
            let val = i < 10 ? '0' + i : i;
            d.innerHTML += `<option value="${val}">${val}</option>`;
        }
    });

    months.forEach((m) => {
        monthNames.forEach((name, index) => {
            m.innerHTML += `<option value="${index}">${name}</option>`;
        });
    });

    years.forEach(y => {
        for(let i=2000; i<=2100; i++) {
            y.innerHTML += `<option value="${i}">${i}</option>`;
        }
        y.value = 2026; // Default to current year
    });
}

fillDropdowns();

function calculate() {
    let P = parseFloat(document.getElementById('principal').value);
    let r = parseFloat(document.getElementById('rate').value) / 100;
    
    let start = new Date(document.getElementById('startYear').value, document.getElementById('startMonth').value, document.getElementById('startDay').value);
    let end = new Date(document.getElementById('endYear').value, document.getElementById('endMonth').value, document.getElementById('endDay').value);
    
    if (isNaN(P)) { alert("Enter Amount"); return; }
    let diffDays = Math.floor((end - start) / (1000 * 60 * 60 * 24));
    if (diffDays < 0) { alert("End date must be after Start date"); return; }

    let fullMonths = Math.floor(diffDays / 30);
    let extraDays = diffDays % 30;
    let calcMonths = fullMonths + (extraDays >= 4 ? 0.5 : 0);

    let total, tag;
    if (calcMonths <= 12) {
        total = P + (P * r * calcMonths);
        tag = { text: "GOOD: SIMPLE INTEREST", class: "status-tag good" };
    } else {
        total = P * Math.pow((1 + r), calcMonths);
        tag = { text: "LATE: COMPOUND INTEREST", class: "status-tag late" };
    }

    document.getElementById('timeText').innerText = `${fullMonths} Mo, ${extraDays} Days (${calcMonths} for calc)`;
    document.getElementById('interestAmt').innerText = "₹" + (total - P).toLocaleString('en-IN', {maximumFractionDigits: 2});
    document.getElementById('totalAmt').innerText = "₹" + total.toLocaleString('en-IN', {maximumFractionDigits: 2});
    document.getElementById('statusTag').innerText = tag.text;
    document.getElementById('statusTag').className = tag.class;
    document.getElementById('resultBox').style.display = 'block';
}
</script>

</body>
</html>
