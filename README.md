
<!DOCTYPE html>
<html>
<head>
    <title>Saloni Finance Pro Calculator</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #0f172a; padding: 20px; display: flex; justify-content: center; color: #333; }
        .calc-card { background: white; padding: 30px; border-radius: 16px; box-shadow: 0 10px 25px rgba(0,0,0,0.3); width: 400px; }
        h2 { color: #1e293b; text-align: center; margin-top: 0; border-bottom: 2px solid #e2e8f0; padding-bottom: 10px; }
        label { display: block; margin: 15px 0 5px; font-weight: 600; font-size: 14px; color: #475569; }
        .date-row { display: flex; gap: 8px; margin-bottom: 10px; }
        .date-group { flex: 1; }
        .date-label { font-size: 10px; color: #94a3b8; text-transform: uppercase; margin-bottom: 2px; display: block; }
        input, select { width: 100%; padding: 10px; border: 2px solid #e2e8f0; border-radius: 8px; box-sizing: border-box; font-size: 14px; }
        select { cursor: pointer; background: #fdfdfd; }
        .rule-info { font-size: 11px; color: #64748b; margin-top: 5px; font-style: italic; line-height: 1.4; }
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

    <label>Start Date (DD-MM-YYYY)</label>
    <div class="date-row">
        <div class="date-group"><span class="date-label">Day</span><select id="startDay"></select></div>
        <div class="date-group"><span class="date-label">Month</span><select id="startMonth"></select></div>
        <div class="date-group"><span class="date-label">Year</span><select id="startYear"></select></div>
    </div>

    <label>End Date (Settlement)</label>
    <div class="date-row">
        <div class="date-group"><span class="date-label">Day</span><select id="endDay"></select></div>
        <div class="date-group"><span class="date-label">Month</span><select id="endMonth"></select></div>
        <div class="date-group"><span class="date-label">Year</span><select id="endYear"></select></div>
    </div>

    <label>Monthly Interest Rate (%)</label>
    <input type="number" id="rate" value="2.5">

    <p class="rule-info">Rule: 4+ Days = +0.5 Month.<br>13+ Months = Compound Interest (LATE).</p>

    <button onclick="calculate()">Calculate Settlement</button>

    <div id="resultBox" class="result">
        <div id="statusTag" class="status-tag"></div>
        <div style="font-size: 13px; color: #64748b;">Time Taken: <span id="timeText" style="font-weight:bold; color:#1e293b;"></span></div>
        <hr style="border:0; border-top:1px solid #e2e8f0; margin:10px 0;">
        <div style="font-size: 13px; color: #64748b;">Total Interest:</div>
        <div id="interestAmt" style="font-size: 20px; font-weight: bold; color: #1e293b; margin-bottom: 10px;"></div>
        <div style="font-size: 13px; color: #64748b;">Final Amount:</div>
        <div id="totalAmt" style="font-size: 26px; font-weight: 800; color: #2563eb;"></div>
    </div>
</div>

<script>
// Automatically fill numbers into dropdowns
function fillDropdowns() {
    const days = document.querySelectorAll('#startDay, #endDay');
    const months = document.querySelectorAll('#startMonth, #endMonth');
    const years = document.querySelectorAll('#startYear, #endYear');

    // Fill Days 01-31
    days.forEach(d => {
        for(let i=1; i<=31; i++) {
            let val = i < 10 ? '0' + i : i;
            d.innerHTML += `<option value="${val}">${val}</option>`;
        }
    });

    // Fill Months 01-12
    months.forEach(m => {
        for(let i=1; i<=12; i++) {
            let val = i < 10 ? '0' + i : i;
            m.innerHTML += `<option value="${i-
