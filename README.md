<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>QPS Per Pod Calculator</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 1400px;
            margin: 0 auto;
            background: white;
            border-radius: 12px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            padding: 30px;
        }
        
        h1 {
            color: #333;
            margin-bottom: 10px;
            font-size: 28px;
        }
        
        .subtitle {
            color: #666;
            margin-bottom: 30px;
            font-size: 14px;
        }
        
        .input-section {
            background: #f8f9fa;
            padding: 20px;
            border-radius: 8px;
            margin-bottom: 30px;
            border-left: 4px solid #667eea;
        }
        
        .input-group {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-bottom: 20px;
        }
        
        .input-field {
            display: flex;
            flex-direction: column;
        }
        
        label {
            font-weight: 600;
            color: #333;
            margin-bottom: 8px;
            font-size: 14px;
        }
        
        input[type="number"] {
            padding: 12px;
            border: 2px solid #e0e0e0;
            border-radius: 6px;
            font-size: 14px;
            transition: border-color 0.3s;
        }
        
        input[type="number"]:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }
        
        .button-group {
            display: flex;
            gap: 10px;
            margin-top: 20px;
            flex-wrap: wrap;
        }
        
        button {
            padding: 12px 24px;
            border: none;
            border-radius: 6px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            font-size: 14px;
        }
        
        .btn-calculate {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            flex: 1;
            min-width: 150px;
        }
        
        .btn-calculate:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(102, 126, 234, 0.3);
        }
        
        .btn-reset {
            background: #e0e0e0;
            color: #333;
            flex: 1;
            min-width: 150px;
        }
        
        .btn-reset:hover {
            background: #d0d0d0;
        }

        .btn-add-request {
            background: #4caf50;
            color: white;
            flex: 1;
            min-width: 150px;
        }

        .btn-add-request:hover {
            background: #45a049;
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(76, 175, 80, 0.3);
        }
        
        .results-section {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        
        .result-card {
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            padding: 20px;
            border-radius: 8px;
            border-left: 4px solid #667eea;
        }
        
        .result-card.warning {
            border-left-color: #ff9800;
            background: linear-gradient(135deg, #fff3e0 0%, #ffe0b2 100%);
        }
        
        .result-card.success {
            border-left-color: #4caf50;
            background: linear-gradient(135deg, #f1f8e9 0%, #dcedc8 100%);
        }
        
        .result-label {
            color: #666;
            font-size: 12px;
            font-weight: 600;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            margin-bottom: 8px;
        }
        
        .result-value {
            font-size: 28px;
            font-weight: 700;
            color: #333;
        }
        
        .result-unit {
            color: #999;
            font-size: 12px;
            margin-left: 5px;
        }
        
        .result-info {
            font-size: 12px;
            color: #666;
            margin-top: 8px;
        }
        
        .table-section {
            margin-top: 40px;
        }
        
        .table-section h2 {
            color: #333;
            margin-bottom: 15px;
            font-size: 18px;
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
            border-radius: 8px;
            overflow: hidden;
        }
        
        thead {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }
        
        th {
            padding: 16px;
            text-align: left;
            font-weight: 600;
            font-size: 14px;
            border-bottom: 2px solid #667eea;
        }
        
        td {
            padding: 14px 16px;
            border-bottom: 1px solid #e0e0e0;
            font-size: 14px;
        }
        
        tbody tr {
            transition: background-color 0.3s;
        }
        
        tbody tr:hover {
            background-color: #f8f9fa;
        }
        
        tbody tr:last-child td {
            border-bottom: 2px solid #667eea;
        }
        
        .status-badge {
            display: inline-block;
            padding: 4px 12px;
            border-radius: 20px;
            font-weight: 600;
            font-size: 12px;
        }
        
        .status-ok {
            background-color: #c8e6c9;
            color: #2e7d32;
        }
        
        .status-warning {
            background-color: #ffccbc;
            color: #d84315;
        }
        
        .recommendation {
            background: #e3f2fd;
            border-left: 4px solid #2196f3;
            padding: 15px;
            border-radius: 6px;
            margin-top: 20px;
            font-size: 14px;
            color: #1565c0;
            line-height: 1.6;
        }
        
        .recommendation strong {
            display: block;
            margin-bottom: 8px;
            color: #0d47a1;
        }
        
        .recommendation li {
            margin-left: 20px;
            margin-top: 5px;
        }
        
        .history-section {
            margin-top: 40px;
            background: #f8f9fa;
            padding: 20px;
            border-radius: 8px;
        }
        
        .history-section h2 {
            color: #333;
            margin-bottom: 15px;
            font-size: 18px;
        }
        
        .history-table {
            width: 100%;
            border-collapse: collapse;
            overflow-x: auto;
            display: block;
        }
        
        .history-table th {
            background: #e0e0e0;
            padding: 12px;
            text-align: left;
            font-weight: 600;
            border-bottom: 2px solid #bdbdbd;
            font-size: 13px;
        }
        
        .history-table td {
            padding: 12px;
            border-bottom: 1px solid #e0e0e0;
            font-size: 13px;
        }
        
        .history-table tbody tr:hover {
            background: #eeeeee;
        }

        .highlight {
            background-color: #fff3cd;
            font-weight: 600;
            color: #856404;
        }

        .increase-input-group {
            display: grid;
            grid-template-columns: 1fr auto;
            gap: 10px;
            align-items: flex-end;
        }

        .increase-input-group input {
            margin-bottom: 0;
        }

        .info-box {
            background: #e8f4f8;
            border-left: 4px solid #03a9f4;
            padding: 15px;
            border-radius: 6px;
            margin-bottom: 20px;
            font-size: 13px;
            color: #01579b;
            line-height: 1.6;
        }

        .info-box strong {
            color: #004d7a;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎯 QPS Per Pod Calculator</h1>
        <p class="subtitle">Equally distribute QPS. Increase replicas linearly when per-pod QPS exceeds 24k</p>
        
        <div class="info-box">
            <strong>Distribution Logic:</strong>
            Increase QPS is equally split between Direct and Indirect traffic. Each pod receives equal QPS. 
            When per-pod QPS exceeds 24k, replicas are increased linearly on both sides to bring it within range.
        </div>
        
        <div class="input-section">
            <div class="input-group">
                <div class="input-field">
                    <label for="directQPS">Direct Traffic QPS (in thousands)</label>
                    <input type="number" id="directQPS" placeholder="e.g., 100.5" value="100" step="0.1">
                </div>
                <div class="input-field">
                    <label for="indirectQPS">Indirect Traffic QPS (in thousands)</label>
                    <input type="number" id="indirectQPS" placeholder="e.g., 60.5" value="60" step="0.1">
                </div>
                <div class="input-field">
                    <label for="currentDirectReplicas">Current Direct Replicas</label>
                    <input type="number" id="currentDirectReplicas" placeholder="e.g., 5" value="5" step="1" min="1">
                </div>
                <div class="input-field">
                    <label for="currentIndirectReplicas">Current Indirect Replicas</label>
                    <input type="number" id="currentIndirectReplicas" placeholder="e.g., 3" value="3" step="1" min="1">
                </div>
            </div>

            <div class="input-group">
                <div class="input-field">
                    <label for="minQPS">Minimum QPS Per Pod (k)</label>
                    <input type="number" id="minQPS" placeholder="e.g., 18" value="18" step="1">
                </div>
                <div class="input-field">
                    <label for="maxQPS">Maximum QPS Per Pod (k)</label>
                    <input type="number" id="maxQPS" placeholder="e.g., 24" value="24" step="1">
                </div>
                <div class="input-field increase-input-group">
                    <div>
                        <label for="increaseQPS">Increase QPS Request (in thousands)</label>
                        <input type="number" id="increaseQPS" placeholder="e.g., 30" value="0" step="0.1">
                    </div>
                    <button class="btn-add-request" onclick="window.addIncreaseRequest()">+ Add</button>
                </div>
            </div>

            <div class="button-group">
                <button class="btn-calculate" onclick="window.calculateOptimal()">Calculate Distribution</button>
                <button class="btn-reset" onclick="window.resetAll()">Reset All</button>
            </div>
        </div>
        
        <div class="results-section" id="resultsSection">
            <div class="result-card">
                <div class="result-label">Total QPS</div>
                <div class="result-value"><span id="displayTotalQPS">160</span><span class="result-unit">k</span></div>
                <div class="result-info">Direct + Indirect</div>
            </div>
            <div class="result-card">
                <div class="result-label">QPS Per Pod</div>
                <div class="result-value highlight"><span id="qpsPerPod">0</span><span class="result-unit">k</span></div>
                <div class="result-info">Equal distribution to all pods</div>
            </div>
            <div class="result-card">
                <div class="result-label">Total Replicas</div>
                <div class="result-value"><span id="totalReplicas">0</span></div>
                <div class="result-info">Direct + Indirect</div>
            </div>
            <div class="result-card">
                <div class="result-label">Status</div>
                <div class="result-value"><span id="statusText">-</span></div>
                <div class="result-info">Replica adjustment</div>
            </div>
        </div>
        
        <div class="table-section">
            <h2>📊 Traffic Distribution (Equal QPS per Pod)</h2>
            <table>
                <thead>
                    <tr>
                        <th>Traffic Type</th>
                        <th>Total QPS (k)</th>
                        <th>Current Replicas</th>
                        <th>Current QPS/Pod (k)</th>
                        <th>Needed Replicas</th>
                        <th>Final QPS/Pod (k)</th>
                        <th>Action</th>
                        <th>Status</th>
                    </tr>
                </thead>
                <tbody id="trafficTable">
                </tbody>
            </table>
        </div>
        
        <div id="recommendationDiv"></div>
        
        <div class="history-section">
            <h2>📜 Calculation History</h2>
            <table class="history-table">
                <thead>
                    <tr>
                        <th>Timestamp</th>
                        <th>Direct (k)</th>
                        <th>Indirect (k)</th>
                        <th>Total (k)</th>
                        <th>Current Dir Replicas</th>
                        <th>Current Ind Replicas</th>
                        <th>Final Dir Replicas</th>
                        <th>Final Ind Replicas</th>
                        <th>QPS/Pod (k)</th>
                        <th>Status</th>
                        <th>Action</th>
                    </tr>
                </thead>
                <tbody id="historyTable">
                    <tr><td colspan="11" style="text-align: center; color: #999;">No calculations yet</td></tr>
                </tbody>
            </table>
        </div>
    </div>
    
    <script>
        let calculationHistory = [];
        
        window.addIncreaseRequest = function() {
            const increaseQPS = parseFloat(document.getElementById('increaseQPS').value) || 0;
            
            if (increaseQPS <= 0) {
                alert('Please enter a positive QPS increase value');
                return;
            }
            
            const directQPS = parseFloat(document.getElementById('directQPS').value) || 0;
            const indirectQPS = parseFloat(document.getElementById('indirectQPS').value) || 0;
            
            // Equally divide increase between direct and indirect
            const increasePerType = increaseQPS / 2;
            
            const newDirectQPS = directQPS + increasePerType;
            const newIndirectQPS = indirectQPS + increasePerType;
            
            document.getElementById('directQPS').value = (Math.round(newDirectQPS * 10) / 10).toFixed(1);
            document.getElementById('indirectQPS').value = (Math.round(newIndirectQPS * 10) / 10).toFixed(1);
            document.getElementById('increaseQPS').value = '0';
            
            window.calculateOptimal();
        };
        
        window.calculateOptimal = function() {
            const directQPS = parseFloat(document.getElementById('directQPS').value) || 0;
            const indirectQPS = parseFloat(document.getElementById('indirectQPS').value) || 0;
            const currentDirectReplicas = parseInt(document.getElementById('currentDirectReplicas').value) || 1;
            const currentIndirectReplicas = parseInt(document.getElementById('currentIndirectReplicas').value) || 1;
            const minQPS = parseFloat(document.getElementById('minQPS').value) || 18;
            const maxQPS = parseFloat(document.getElementById('maxQPS').value) || 24;
            
            const totalQPS = directQPS + indirectQPS;
            const currentTotalReplicas = currentDirectReplicas + currentIndirectReplicas;
            const currentQpsPerPod = totalQPS / currentTotalReplicas;
            
            if (totalQPS <= 0) {
                alert('Please enter valid QPS values');
                return;
            }
            
            // Calculate needed replicas for EQUAL distribution
            // All pods must receive SAME QPS regardless of traffic type
            let neededTotalReplicas = currentTotalReplicas;
            let neededDirectReplicas = currentDirectReplicas;
            let neededIndirectReplicas = currentIndirectReplicas;
            
            const targetQpsPerPod = totalQPS / neededTotalReplicas;
            
            // If target QPS per pod exceeds max, scale up both equally
            if (targetQpsPerPod > maxQPS) {
                let scalingFactor = 0;
                let foundSolution = false;
                
                while (!foundSolution && scalingFactor < 100) {
                    neededDirectReplicas = currentDirectReplicas + scalingFactor;
                    neededIndirectReplicas = currentIndirectReplicas + scalingFactor;
                    neededTotalReplicas = neededDirectReplicas + neededIndirectReplicas;
                    
                    const testQpsPerPod = totalQPS / neededTotalReplicas;
                    
                    // Check if QPS per pod is within range
                    if (testQpsPerPod >= minQPS && testQpsPerPod <= maxQPS) {
                        foundSolution = true;
                    } else {
                        scalingFactor++;
                    }
                }
            }
            
            const actualQpsPerPod = totalQPS / neededTotalReplicas;
            
            const actualDirectQpsPerPod = actualQpsPerPod;
            const actualIndirectQpsPerPod = actualQpsPerPod;
            
            // Determine status
            let status = 'NO CHANGE NEEDED';
            let statusClass = 'success';
            let action = '';
            
            if (neededTotalReplicas > currentTotalReplicas) {
                status = 'SCALED UP';
                statusClass = 'warning';
                const addDirectReplicas = neededDirectReplicas - currentDirectReplicas;
                const addIndirectReplicas = neededIndirectReplicas - currentIndirectReplicas;
                action = `Add ${addDirectReplicas} direct + ${addIndirectReplicas} indirect`;
            } else {
                action = 'No change needed';
            }
            
            // Update display
            document.getElementById('displayTotalQPS').textContent = totalQPS.toFixed(1);
            document.getElementById('qpsPerPod').textContent = actualQpsPerPod.toFixed(2);
            document.getElementById('totalReplicas').textContent = neededTotalReplicas;
            document.getElementById('statusText').textContent = status;
            
            // Update result card styling
            const statusCard = document.querySelectorAll('.result-card')[3];
            statusCard.className = `result-card ${statusClass}`;
            
            // Update traffic table
            updateTrafficTable(directQPS, indirectQPS, currentDirectReplicas, currentIndirectReplicas,
                             neededDirectReplicas, neededIndirectReplicas, actualDirectQpsPerPod, 
                             actualIndirectQpsPerPod, minQPS, maxQPS, currentQpsPerPod);
            
            // Generate recommendation
            generateRecommendation(directQPS, indirectQPS, currentDirectReplicas, currentIndirectReplicas,
                                 neededDirectReplicas, neededIndirectReplicas, actualQpsPerPod, 
                                 actualDirectQpsPerPod, actualIndirectQpsPerPod, maxQPS);
            
            // Add to history
            addToHistory(directQPS, indirectQPS, totalQPS, currentDirectReplicas, currentIndirectReplicas,
                        neededDirectReplicas, neededIndirectReplicas, actualQpsPerPod, status, action);
        };
        
        function updateTrafficTable(directQPS, indirectQPS, currentDirectReplicas, currentIndirectReplicas,
                                   neededDirectReplicas, neededIndirectReplicas, actualDirectQpsPerPod,
                                   actualIndirectQpsPerPod, minQPS, maxQPS, currentQpsPerPod) {
            const tbody = document.getElementById('trafficTable');
            
            const getStatusClass = (qps) => {
                if (qps >= minQPS && qps <= maxQPS) return 'status-ok';
                return 'status-warning';
            };
            
            const getStatusText = (qps) => {
                if (qps >= minQPS && qps <= maxQPS) return '✓ OK';
                return '⚠ OUT OF RANGE';
            };
            
            const getActionText = (current, needed) => {
                if (current === needed) return 'No change';
                return current < needed ? `Add ${needed - current}` : `Remove ${current - needed}`;
            };
            
            const totalQPS = directQPS + indirectQPS;
            const currentTotalReplicas = currentDirectReplicas + currentIndirectReplicas;
            const neededTotalReplicas = neededDirectReplicas + neededIndirectReplicas;
            const finalQpsPerPod = totalQPS / neededTotalReplicas;
            
            tbody.innerHTML = `
                <tr>
                    <td><strong>Direct Traffic</strong></td>
                    <td>${directQPS.toFixed(1)}k</td>
                    <td>${currentDirectReplicas}</td>
                    <td>${(directQPS / currentDirectReplicas).toFixed(2)}k</td>
                    <td><strong>${neededDirectReplicas}</strong></td>
                    <td class="highlight">${actualDirectQpsPerPod.toFixed(2)}k</td>
                    <td>${getActionText(currentDirectReplicas, neededDirectReplicas)}</td>
                    <td><span class="status-badge ${getStatusClass(actualDirectQpsPerPod)}">${getStatusText(actualDirectQpsPerPod)}</span></td>
                </tr>
                <tr>
                    <td><strong>Indirect Traffic</strong></td>
                    <td>${indirectQPS.toFixed(1)}k</td>
                    <td>${currentIndirectReplicas}</td>
                    <td>${(indirectQPS / currentIndirectReplicas).toFixed(2)}k</td>
                    <td><strong>${neededIndirectReplicas}</strong></td>
                    <td class="highlight">${actualIndirectQpsPerPod.toFixed(2)}k</td>
                    <td>${getActionText(currentIndirectReplicas, neededIndirectReplicas)}</td>
                    <td><span class="status-badge ${getStatusClass(actualIndirectQpsPerPod)}">${getStatusText(actualIndirectQpsPerPod)}</span></td>
                </tr>
                <tr style="font-weight: 600; background: #f0f0f0;">
                    <td>Total</td>
                    <td>${totalQPS.toFixed(1)}k</td>
                    <td>${currentTotalReplicas}</td>
                    <td>${currentQpsPerPod.toFixed(2)}k</td>
                    <td><strong>${neededTotalReplicas}</strong></td>
                    <td class="highlight">${finalQpsPerPod.toFixed(2)}k</td>
                    <td><strong>Add ${neededTotalReplicas - currentTotalReplicas}</strong></td>
                    <td></td>
                </tr>
            `;
        }
        
        function generateRecommendation(directQPS, indirectQPS, currentDirectReplicas, currentIndirectReplicas,
                                       neededDirectReplicas, neededIndirectReplicas, actualQpsPerPod,
                                       actualDirectQpsPerPod, actualIndirectQpsPerPod, maxQPS) {
            const div = document.getElementById('recommendationDiv');
            const currentTotalReplicas = currentDirectReplicas + currentIndirectReplicas;
            const neededTotalReplicas = neededDirectReplicas + neededIndirectReplicas;
            const totalQPS = directQPS + indirectQPS;
            const currentQpsPerPod = totalQPS / currentTotalReplicas;
            
            let recommendation = '';
            
            if (neededTotalReplicas > currentTotalReplicas) {
                const addDirectReplicas = neededDirectReplicas - currentDirectReplicas;
                const addIndirectReplicas = neededIndirectReplicas - currentIndirectReplicas;
                const addTotal = neededTotalReplicas - currentTotalReplicas;
                
                recommendation = `
                    <strong>⚠️ Scale Up Required:</strong>
                    <ul>
                        <li>Current QPS per pod: <strong>${currentQpsPerPod.toFixed(2)}k</strong> (exceeds max of ${maxQPS}k)</li>
                        <li>Total QPS: <strong>${totalQPS.toFixed(1)}k</strong></li>
                        <li>Add <strong>${addTotal} replica${addTotal !== 1 ? 's' : ''}</strong> total (linearly across both traffic types)</li>
                        <li>Direct traffic: ${currentDirectReplicas} → <strong>${neededDirectReplicas}</strong> replicas (add ${addDirectReplicas})</li>
                        <li>Indirect traffic: ${currentIndirectReplicas} → <strong>${neededIndirectReplicas}</strong> replicas (add ${addIndirectReplicas})</li>
                        <li><strong>After scaling:</strong> Each pod will receive <strong>${actualQpsPerPod.toFixed(2)}k</strong> QPS (within 18k-24k range)</li>
                        <li>Direct: ${directQPS.toFixed(1)}k ÷ ${neededDirectReplicas} = ${actualDirectQpsPerPod.toFixed(2)}k per pod</li>
                        <li>Indirect: ${indirectQPS.toFixed(1)}k ÷ ${neededIndirectReplicas} = ${actualIndirectQpsPerPod.toFixed(2)}k per pod</li>
                    </ul>
                `;
            } else {
                recommendation = `
                    <strong>✅ Balanced Configuration:</strong>
                    <ul>
                        <li>Current configuration is optimal</li>
                        <li>Total QPS: <strong>${totalQPS.toFixed(1)}k</strong></li>
                        <li>Current replicas: <strong>${currentTotalReplicas}</strong> (${currentDirectReplicas} direct + ${currentIndirectReplicas} indirect)</li>
                        <li>Each pod receives equal QPS: <strong>${actualQpsPerPod.toFixed(2)}k</strong> (within 18k-24k range)</li>
                        <li>Direct: ${directQPS.toFixed(1)}k ÷ ${neededDirectReplicas} = ${actualDirectQpsPerPod.toFixed(2)}k per pod</li>
                        <li>Indirect: ${indirectQPS.toFixed(1)}k ÷ ${neededIndirectReplicas} = ${actualIndirectQpsPerPod.toFixed(2)}k per pod</li>
                    </ul>
                `;
            }
            
            div.innerHTML = `<div class="recommendation">${recommendation}</div>`;
        }
        
        function addToHistory(directQPS, indirectQPS, totalQPS, currentDirectReplicas, currentIndirectReplicas,
                            neededDirectReplicas, neededIndirectReplicas, actualQpsPerPod, status, action) {
            const now = new Date().toLocaleTimeString();
            calculationHistory.unshift({
                time: now,
                directQPS: directQPS,
                indirectQPS: indirectQPS,
                totalQPS: totalQPS,
                currentDirectReplicas: currentDirectReplicas,
                currentIndirectReplicas: currentIndirectReplicas,
                neededDirectReplicas: neededDirectReplicas,
                neededIndirectReplicas: neededIndirectReplicas,
                qpsPerPod: actualQpsPerPod.toFixed(2),
                status: status,
                action: action
            });
            
            // Keep only last 15 calculations
            if (calculationHistory.length > 15) {
                calculationHistory.pop();
            }
            
            updateHistoryTable();
        }
        
        function updateHistoryTable() {
            const tbody = document.getElementById('historyTable');
            
            if (calculationHistory.length === 0) {
                tbody.innerHTML = '<tr><td colspan="11" style="text-align: center; color: #999;">No calculations yet</td></tr>';
                return;
            }
            
            tbody.innerHTML = calculationHistory.map((entry, idx) => `
                <tr>
                    <td>${entry.time}</td>
                    <td>${entry.directQPS.toFixed(1)}k</td>
                    <td>${entry.indirectQPS.toFixed(1)}k</td>
                    <td><strong>${entry.totalQPS.toFixed(1)}k</strong></td>
                    <td>${entry.currentDirectReplicas}</td>
                    <td>${entry.currentIndirectReplicas}</td>
                    <td><strong>${entry.neededDirectReplicas}</strong></td>
                    <td><strong>${entry.neededIndirectReplicas}</strong></td>
                    <td>${entry.qpsPerPod}k</td>
                    <td>${entry.status}</td>
                    <td>${entry.action}</td>
                </tr>
            `).join('');
        }
        
        window.resetAll = function() {
            document.getElementById('directQPS').value = '100';
            document.getElementById('indirectQPS').value = '60';
            document.getElementById('currentDirectReplicas').value = '5';
            document.getElementById('currentIndirectReplicas').value = '3';
            document.getElementById('minQPS').value = '18';
            document.getElementById('maxQPS').value = '24';
            document.getElementById('increaseQPS').value = '0';
            document.getElementById('displayTotalQPS').textContent = '160';
            document.getElementById('qpsPerPod').textContent = '0';
            document.getElementById('totalReplicas').textContent = '0';
            document.getElementById('statusText').textContent = '-';
            document.getElementById('trafficTable').innerHTML = '';
            document.getElementById('recommendationDiv').innerHTML = '';
        };
        
        // Initial calculation
        window.calculateOptimal();
    </script>
</body>
</html>
