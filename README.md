<!DOCTYPE html>
<html>
<head>
    <title>Saloni Finance Pro Calculator</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #0f172a; padding: 20px; display: flex; justify-content: center; color: #333; }
        .calc-card { background: white; padding: 30px; border-radius: 16px; box-shadow: 0 10px 25px rgba(0,0,0,0.3); width: 380px; }
        h2 { color: #1e293b; text-align: center; margin-top: 0; border-bottom: 2px solid #e2e8f0; padding-bottom: 10px; }
        label { display: block; margin: 15px 0 5px; font-weight: 600; font-size: 14px; }
        input, select { width: 100%; padding: 12px; margin-bottom: 10px; border: 2px solid #e2e8f0; border-radius: 8px; box-sizing: border-box; font-size: 15px; }
        .rule-info { font-size: 12px; color: #64748b; margin-bottom: 15px; font-style: italic; line-height: 1.4; }
        button { width: 100%; padding: 14px; background: #2563eb; color: white; border: none; border-radius: 8px; cursor: pointer; font-size: 16px; font-weight: bold; margin-top: 10px; }
        button:hover { background: #1d4ed8; }
        .result { margin-top: 25px; padding: 20px; border-radius: 10px; background: #f8fafc; border: 1px solid #e2e8f0; display: none; }
        .status-tag { display: inline-block; padding: 4px 10px; border-radius: 20px; font-size: 12px; margin-bottom: 10px; font-weight: bold; }
        .late { background: #fee2e2; color: #991b1b; }
        .good { background: #dcfce7; color: #166534; }
        .simple { background: #e0f2fe; color: #075985; }
    </style>
</head>
<body>

<div class="calc-card">
    <h2>Saloni Finance</h2>
    
    <label>Principal Amount (₹)</label>
    <input type="number" id="principal" placeholder="Enter Amount">

    <label>Start Date</label>
    <input type="date" id="startDate">

    <label>End Date (Settlement)</label>
    <input type="date" id="endDate">

    <label>Interest Rule</label>
    <select id="calcRule">
        <option value="business">Business Rule (SI then CI after 12m)</option>
        <option value="onlySimple">Simple Interest (Always)</option>
    </select>
    <p class="rule-info">Rule: 4+ extra days = +0.5 month.<br>Business Rule: Compound Interest starts at 13 months.</p>

    <label>Monthly Rate (%)</label>
    <input type="number" id="rate" value="2" step="0.1">

    <button onclick="calculate()">Calculate Settlement</button>

    <div id="resultBox" class="result">
        <div id="statusTag" class="status-tag"></div>
        <div style="font-size: 14px; color: #64748b;">Total Time: <span id="timeText" style="font-weight:bold; color:#1e293b;"></span></div>
        <hr style="border:0; border-top:1px solid #e2e8f0; margin:10px 0;">
        <div style="font-size: 14px; color: #64748b;">Interest Earned:</div>
        <div id="interestAmt" style="font-size: 20px; font-weight: bold; color: #1e293b; margin-bottom: 10px;"></div>
        <div style="font-size: 14px; color: #64748b;">Final Settlement:</div>
        <div id="totalAmt" style="font-size: 28px; font-weight: 800; color: #2563eb;"></div>
    </div>
</div>

<script>
    function calculate() {
        let P = parseFloat(document.getElementById('principal').value);
        let r = parseFloat(document.getElementById('rate').value) / 100;
        let start = new Date(document.getElementById('startDate').value);
        let end = new Date(document.getElementById('endDate').value);
        let rule = document.getElementById('calcRule').value;
        
        if (isNaN(P) || isNaN(start.getTime()) || isNaN(end.getTime())) { 
            alert("Please enter Amount and both Dates"); return; 
        }

        // Calculate Months and Days
        let diffTime = end - start;
        let diffDays = Math.floor(diffTime / (1000 * 60 * 60 * 24));
        
        if (diffDays < 0) { alert("End date must be after Start date"); return; }

        let fullMonths = Math.floor(diffDays / 30);
        let remainingDays = diffDays % 30;
        let finalMonths = fullMonths;

        // THE 4-DAY RULE: If 4+ days, add 0.5 months
        if (remainingDays >= 4) {
            finalMonths += 0.5;
        }

        let total;
        let tagText;
        let tagClass;

        if (rule === 'onlySimple') {
            total = P + (P * r * finalMonths);
            tagText = "SIMPLE INTEREST ONLY";
            tagClass = "status-tag simple";
        } else {
            if (finalMonths <= 12) {
                total = P + (P * r * finalMonths);
                tagText = "ON-TIME: SIMPLE INTEREST";
                tagClass = "status-tag good";
            } else {
                // Compound Interest formula for fractional months
                total = P * Math.pow((1 + r), finalMonths);
                tagText = "LATE: COMPOUND INTEREST";
                tagClass = "status-tag late";
            }
        }

        document.getElementById('timeText').innerText = fullMonths + " Months, " + remainingDays + " Days (" + finalMonths + " Mo for calc)";
        document.getElementById('interestAmt').innerText = "₹" + (total - P).toLocaleString('en-IN', {maximumFractionDigits: 2});
        document.getElementById('totalAmt').innerText = "₹" + total.toLocaleString('en-IN', {maximumFractionDigits: 2});
        
        const tag = document.getElementById('statusTag');
        tag.innerText = tagText;
        tag.className = tagClass;
        
        document.getElementById('resultBox').style.display = 'block';
    }
</script>

</body>
</html>
