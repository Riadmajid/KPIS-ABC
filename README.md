<!DOCTYPE html>
<html lang="fr">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PET Dashboard V17.1 - Master Notes</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.0/font/bootstrap-icons.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            --primary: #2c3e50;
            --accent: #3498db;
        }

        body {
            background-color: #f0f2f5;
            font-family: 'Segoe UI', sans-serif;
        }

        .navbar {
            background: var(--primary);
            color: white;
        }

        .card {
            border-radius: 12px;
            border: none;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.05);
        }

        .obs-input {
            display: none;
            margin-top: 5px;
            font-size: 0.75rem;
            border-color: #ffe082;
        }

        .btn-obs-toggle {
            cursor: pointer;
            color: #adb5bd;
            transition: 0.3s;
        }

        .btn-obs-toggle.active {
            color: #f1c40f;
        }

        .stat-item {
            display: flex;
            justify-content: space-between;
            padding: 10px;
            border-bottom: 1px solid #eee;
            cursor: pointer;
            transition: 0.2s;
        }

        .stat-item:hover {
            background-color: #f8f9fa;
            transform: scale(1.02);
        }

        .stat-label {
            font-weight: 500;
            color: #444;
        }

        .stat-value {
            font-weight: bold;
            color: #e74c3c;
        }

        .clickable-card {
            cursor: pointer;
            transition: 0.3s;
        }

        .clickable-card:hover {
            transform: translateY(-3px);
            box-shadow: 0 6px 20px rgba(231, 76, 60, 0.4);
        }

        .note-entry {
            background: #fdfdfd;
            border-left: 4px solid #e74c3c;
            padding: 10px;
            margin-bottom: 10px;
            border-radius: 4px;
        }

        .note-meta {
            font-size: 0.75rem;
            color: #7f8c8d;
            display: flex;
            justify-content: space-between;
            border-bottom: 1px dashed #eee;
            margin-bottom: 5px;
        }
    </style>
</head>

<body>

    <!-- Modal الملاحظات (يستخدم للعرض الفردي والجماعي) -->
    <div class="modal fade" id="notesViewModal" tabindex="-1">
        <div class="modal-dialog modal-lg modal-dialog-scrollable">
            <div class="modal-content">
                <div class="modal-header bg-dark text-white">
                    <h5 class="modal-title" id="notesModalTitle">Motes</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body" id="notesModalBody">
                    <!-- الملاحظات ستظهر هنا -->
                </div>
            </div>
        </div>
    </div>

    <!-- Modal الإعدادات -->
    <div class="modal fade" id="settingsModal" tabindex="-1">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header bg-primary text-white">
                    <h5 class="modal-title"><i class="bi bi-gear-fill me-2"></i>Configuration & Paramètres (<span
                            id="setModeTitle"></span>)</h5>
                    <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal"></button>
                </div>
                <div class="modal-body">
                    <ul class="nav nav-tabs mb-3" id="settingsTab" role="tablist">
                        <li class="nav-item"><button class="nav-link active" data-bs-toggle="tab"
                                data-bs-target="#tab-products">Produits</button></li>
                        <li class="nav-item"><button class="nav-link" data-bs-toggle="tab" data-bs-target="#tab-refs"
                                id="tabRefLabel">Références</button></li>
                        <li class="nav-item"><button class="nav-link" data-bs-toggle="tab"
                                data-bs-target="#tab-security">Sécurité</button></li>
                        <li class="nav-item"><button class="nav-link" data-bs-toggle="tab"
                                data-bs-target="#tab-objectives">Objectifs Lignes</button></li>
                        <li class="nav-item"><button class="nav-link" data-bs-toggle="tab"
                                data-bs-target="#tab-data">Données</button></li>
                    </ul>
                    <div class="tab-content">
                        <!-- Produits Tab -->
                        <div class="tab-pane fade show active" id="tab-products">
                            <div class="d-flex gap-2 mb-3">
                                <input type="text" id="newProdName" class="form-control form-control-sm"
                                    placeholder="Produit">
                                <input type="number" id="newProdCoeff" step="0.001" class="form-control form-control-sm"
                                    style="width: 80px;" placeholder="Units">
                                <button class="btn btn-sm btn-success" onclick="addProduct()"><i
                                        class="bi bi-plus"></i></button>
                            </div>
                            <div class="table-responsive" style="max-height: 300px;">
                                <table class="table table-sm small">
                                    <thead>
                                        <tr>
                                            <th>Produit</th>
                                            <th>Coeff (Unités)</th>
                                            <th>Action</th>
                                        </tr>
                                    </thead>
                                    <tbody id="settingsProductBody"></tbody>
                                </table>
                            </div>
                        </div>
                        <!-- Références Tab (Dynamique) -->
                        <div class="tab-pane fade" id="tab-refs">
                            <div class="d-flex gap-2 mb-3">
                                <input type="text" id="newRefName" class="form-control form-control-sm"
                                    placeholder="Nouvelle référence">
                                <button class="btn btn-sm btn-success" onclick="addRef()"><i
                                        class="bi bi-plus"></i></button>
                            </div>
                            <div class="table-responsive" style="max-height: 300px;">
                                <table class="table table-sm small text-center">
                                    <thead>
                                        <tr>
                                            <th id="refTableLabel">Référence</th>
                                            <th>Action</th>
                                        </tr>
                                    </thead>
                                    <tbody id="settingsRefBody"></tbody>
                                </table>
                            </div>
                        </div>
                        <!-- Sécurité Tab -->
                        <div class="tab-pane fade" id="tab-security">
                            <div class="p-3 border rounded">
                                <label class="small fw-bold mb-2">Changer le Code PIN (Actuel: <span
                                        id="currentPinRef"></span>)</label>
                                <div class="d-flex gap-2">
                                    <input type="password" id="newPinInput" class="form-control"
                                        placeholder="Nouveau PIN (4 chiffres)">
                                    <button class="btn btn-primary" onclick="changePIN()">Mettre à jour</button>
                                </div>
                            </div>
                        </div>
                        <!-- Objectifs Tab -->
                        <div class="tab-pane fade" id="tab-objectives">
                            <div class="table-responsive" style="max-height: 300px;">
                                <table class="table table-sm small">
                                    <thead>
                                        <tr>
                                            <th>Ligne</th>
                                            <th>Objectif (%)</th>
                                        </tr>
                                    </thead>
                                    <tbody id="settingsObjBody"></tbody>
                                </table>
                            </div>
                            <button class="btn btn-sm btn-primary mt-2" onclick="saveObjectives()">Enregistrer
                                Objectifs</button>
                        </div>
                        <!-- Données Tab -->
                        <div class="tab-pane fade" id="tab-data">
                            <div class="d-grid gap-2">
                                <button class="btn btn-outline-dark" onclick="exportData()"><i
                                        class="bi bi-download me-2"></i>Exporter Historique (JSON)</button>
                                <button class="btn btn-outline-danger" onclick="clearHistory()"><i
                                        class="bi bi-trash me-2"></i>Effacer tout l'historique</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal الأمان -->
    <div class="modal fade" id="authModal" data-bs-backdrop="static" tabindex="-1">
        <div class="modal-dialog modal-dialog-centered modal-sm">
            <div class="modal-content">
                <div class="modal-body text-center p-4">
                    <h6 class="mb-3">Code d'accès requis</h6>
                    <input type="password" id="modalPassInput" class="form-control text-center mb-3" placeholder="****">
                    <button class="btn btn-primary w-100" onclick="confirmAuth()">Valider</button>
                </div>
            </div>
        </div>
    </div>

    <nav class="navbar py-2 mb-4 sticky-top">
        <div class="container-fluid px-4">
            <span class="navbar-brand fw-bold"><i class="bi bi-cpu-fill me-2"></i>PET DASHBOARD V17.1</span>
            <div class="ms-auto me-3 d-flex gap-2">
                <button id="btnModePreform" class="btn btn-sm btn-light active"
                    onclick="switchMode('PREFORM')">PREFORME</button>
                <button id="btnModeCO2" class="btn btn-sm btn-outline-light" onclick="switchMode('CO2')">CO2</button>
            </div>
            <button class="btn btn-sm btn-outline-light" onclick="launchAuth('CONFIG')"><i
                    class="bi bi-gear"></i></button>
        </div>
    </nav>

    <div class="container-fluid px-4">
        <div class="row g-4">
            <!-- قسم الإدخال -->
            <div class="col-xl-3">
                <div class="card p-3 shadow-sm border-top border-4 border-primary">
                    <h6 class="fw-bold mb-3 text-primary border-bottom pb-2 d-flex justify-content-between">
                        <span id="labelSaisie">SAISIE PRODUCTION</span>
                        <span id="badgeSuffix" class="badge bg-secondary small">PREFORME</span>
                    </h6>
                    <form id="prodForm">
                        <div class="mb-2">
                            <input type="date" id="inDate" class="form-control form-control-sm mb-1 shadow-sm">
                            <select id="inLine" class="form-select form-select-sm mb-1 shadow-sm">
                                <!-- Opzioni dinamiche -->
                            </select>
                            <select id="inRef" class="form-select form-select-sm mb-1 shadow-sm">
                                <option value="">Sélectionner Reference...</option>
                            </select>
                            <input list="pList" id="inProd" class="form-control form-control-sm shadow-sm"
                                placeholder="Produit..." oninput="calculateLive()">
                            <datalist id="pList"></datalist>
                        </div>

                        <div class="row g-1 mb-2">
                            <div class="col-12"><label class="small fw-bold">Compteur Packs</label><input type="number"
                                    id="inPacks" class="form-control form-control-sm" oninput="calculateLive()"></div>
                        </div>

                        <!-- Champs spécifiques CO2 -->
                        <div id="co2Fields" style="display:none">
                            <div class="row g-1 mb-2">
                                <div class="col-6"><label class="small fw-bold">Compteur Début</label><input
                                        type="number" id="inStart" class="form-control form-control-sm"
                                        oninput="calculateLive()"></div>
                                <div class="col-6"><label class="small fw-bold">Compteur Fin</label><input type="number"
                                        id="inEnd" class="form-control form-control-sm" oninput="calculateLive()"></div>
                            </div>
                        </div>

                        <!-- Champs spécifiques PREFORM -->
                        <div id="preformFields">
                            <div class="row g-1 mb-2">
                                <div class="col-12"><label class="small fw-bold">Réel (Kg)</label><input type="number"
                                        id="inReal" step="0.001" class="form-control form-control-sm"
                                        oninput="calculateLive()"></div>
                            </div>
                        </div>

                        <div class="bg-light p-2 rounded mb-3 border text-center">
                            <div id="resultLabel" class="small fw-bold text-muted mb-1">Calculs en direct</div>
                            <div class="small">
                                <span id="labelVal1">Écart</span>: <span id="liveVal1" class="fw-bold">0.00</span> |
                                <span id="labelVal2">Reste</span>: <span id="liveVal2"
                                    class="fw-bold text-danger">0.00</span>
                            </div>
                            <div id="liveRendementContainer" class="small mt-1" style="display:none">
                                Yield: <span id="liveYield" class="fw-bold text-success">0.0%</span>
                            </div>
                        </div>

                        <div id="lossSection">
                            <h6 class="small fw-bold text-danger mb-2">PERTES & NOTES 💬</h6>
                            <div class="row g-2" id="lossInputsContainer"></div>
                        </div>

                        <div class="mb-2">
                            <label class="small fw-bold">Note de Production 💬</label>
                            <textarea id="inGeneralNote" class="form-control form-control-sm" rows="2"
                                placeholder="Remarques générales..."></textarea>
                        </div>

                        <div class="d-flex gap-2">
                            <button type="button" class="btn btn-primary flex-grow-1 mt-3 fw-bold shadow" id="btnSave"
                                onclick="launchAuth('SAVE')">ENREGISTRER</button>
                            <button type="button" class="btn btn-secondary mt-3 fw-bold shadow" id="btnCancelEdit"
                                onclick="cancelEdit()" style="display:none">ANNULER</button>
                        </div>
                    </form>
                </div>
            </div>

            <!-- قسم العرض -->
            <div class="col-xl-6">
                <div class="card p-3 mb-3">
                    <div class="d-flex justify-content-between align-items-center mb-2">
                        <div class="d-flex align-items-center gap-2">
                            <select id="fLine" class="form-select form-select-sm w-auto fw-bold" onchange="refreshUI()">
                                <option value="ALL">Toutes les Lignes</option>
                                <option value="CSD">CSD</option>
                                <option value="WATER">WATER</option>
                            </select>
                            <button class="btn btn-sm btn-outline-success fw-bold" onclick="exportExcel()">
                                <i class="bi bi-file-earmark-excel"></i> XLSX
                            </button>
                        </div>
                        <span class="badge bg-info">15 Derniers Rapports</span>
                    </div>
                    <div style="height: 250px;"><canvas id="yieldChart"></canvas></div>
                </div>

                <div class="card shadow-sm overflow-hidden">
                    <table class="table table-sm table-hover align-middle mb-0 text-center">
                        <thead class="table-dark small">
                            <tr>
                                <th>Date</th>
                                <th>Produit</th>
                                <th>Yield</th>
                                <th>Pertes</th>
                                <th>X</th>
                            </tr>
                        </thead>
                        <tbody id="historyBody" class="small"></tbody>
                    </table>
                </div>
            </div>

            <!-- قسم الإحصائيات -->
            <div class="col-xl-3">
                <!-- بطاقة إجمالي الخسائر القابلة للنقر -->
                <div id="p_cumulCard" class="card bg-danger text-white p-3 text-center mb-3 clickable-card shadow"
                    onclick="showAllNotes()">
                    <div class="small opacity-75 text-uppercase fw-bold">Cumul des Pertes</div>
                    <h1 class="fw-bold mb-0" id="totalLosses">0</h1>
                    <div class="small mt-1"><i class="bi bi-eye-fill me-1"></i> Voir toutes les notes</div>
                </div>

                <div id="statsSection" class="card p-3 shadow-sm">
                    <h6 class="fw-bold border-bottom pb-2 small">DÉTAIL PAR MACHINE <span class="text-muted">(Notes
                            individuelles)</span></h6>
                    <div id="statsContainer"></div>

                    <div class="mt-4 p-3 bg-light text-center border-start border-5 border-success rounded-end">
                        <div class="small fw-bold text-muted text-uppercase">Rendement Moyen</div>
                        <h2 class="fw-bold text-success mb-0" id="avgYield">0%</h2>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        let appMode = localStorage.getItem('pet_v17_mode') || "PREFORM";
        let hist = JSON.parse(localStorage.getItem('pet_v17_hist')) || [];
        let appPass = localStorage.getItem('pet_v17_pwd') || "0000";

        // Data for modes
        let productNames = ["CCZ Pet 0,45 L*12", "CC.LGH Pet 2/2*8 PF", "CC.ZE Pet 2/2*4 PF", "CC.ZE Pet 1/2*12", "CC Pet 1,3 L*6", "SP Pet 1/2*12", "Ciel 1/2*12", "Ciel 3/2*8"];
        let dataSets = JSON.parse(localStorage.getItem('pet_v17_datasets')) || {
            PREFORM: {
                prods: productNames.map(n => ({ name: n, coeff: 0.250 })),
                refs: ["28g", "32g", "42g"],
                objectives: { CSD: 95, WATER: 98 }
            },
            CO2: {
                prods: [{ name: "Gaz CO2 Alimentaire", coeff: 1.0 }],
                refs: ["Ref1", "Ref2"],
                objectives: { CSD: 95, CAN: 90, RGB: 92 }
            }
        };
        let activeData = dataSets[appMode];
        let refs = activeData.refs;
        let chart;
        let pendingAction, pendingData;
        let editingId = null;

        const machineKeys = [
            { id: 'ps', label: 'Pref. Souffleuse' },
            { id: 'bs', label: 'Bout. Souffleuse' },
            { id: 'br', label: 'Bout. Rinceuse' },
            { id: 'bt', label: 'Bout. Soutireuse' },
            { id: 'mr', label: 'Mal Remplis' },
            { id: 'la', label: 'Labo / Autre' }
        ];

        window.onload = () => {
            initInputs();
            refreshModeUI();
            document.getElementById('inDate').valueAsDate = new Date();
        };

        function switchMode(mode) {
            appMode = mode;
            localStorage.setItem('pet_v17_mode', appMode);
            activeData = dataSets[appMode];
            prods = activeData.prods;
            refs = activeData.refs;
            refreshModeUI();
        }

        function refreshModeUI() {
            // Update Buttons
            document.getElementById('btnModePreform').className = appMode === 'PREFORM' ? 'btn btn-sm btn-light active' : 'btn btn-sm btn-outline-light';
            document.getElementById('btnModeCO2').className = appMode === 'CO2' ? 'btn btn-sm btn-light active' : 'btn btn-sm btn-outline-light';

            // Labels and Badges
            const isCO2 = appMode === 'CO2';
            document.getElementById('badgeSuffix').innerText = appMode;
            document.getElementById('inRef').parentElement.querySelector('select#inRef option:first-child').innerText = isCO2 ? "Sélectionner CO2 Type..." : "Sélectionner Préforme...";

            // Toggle Form Fields
            document.getElementById('co2Fields').style.display = isCO2 ? 'block' : 'none';
            document.getElementById('preformFields').style.display = isCO2 ? 'none' : 'block';
            document.getElementById('labelVal1').innerText = isCO2 ? "Réel" : "Écart";
            document.getElementById('labelVal2').innerText = isCO2 ? "Théo" : "Reste";
            document.getElementById('liveRendementContainer').style.display = isCO2 ? 'block' : 'none';

            // Update Lines
            const lineSel = document.getElementById('inLine');
            if (isCO2) {
                lineSel.innerHTML = `
                    <option value="CSD">Ligne CSD</option>
                    <option value="CAN">Ligne CAN</option>
                    <option value="RGB">Ligne RGB</option>`;
            } else {
                lineSel.innerHTML = `
                    <option value="CSD">Ligne CSD</option>
                    <option value="WATER">Ligne WATER</option>`;
            }

            // Hide Loss Related Sections in CO2
            document.getElementById('lossSection').style.display = isCO2 ? 'none' : 'block';
            document.getElementById('p_cumulCard').style.display = isCO2 ? 'none' : 'block';
            document.getElementById('statsSection').style.display = isCO2 ? 'none' : 'block';

            initProductList();
            initRefList();
            refreshUI();
        }

        function initInputs() {
            const container = document.getElementById('lossInputsContainer');
            machineKeys.forEach(m => {
                container.innerHTML += `
                <div class="col-6 mb-1">
                    <div class="d-flex justify-content-between mb-1">
                        <label class="small fw-bold" style="font-size:0.65rem">${m.label}</label>
                        <i class="bi bi-chat-dots btn-obs-toggle" id="toggle_${m.id}" onclick="toggleObs('${m.id}')"></i>
                    </div>
                    <input type="number" id="p_${m.id}" class="form-control form-control-sm" value="0" oninput="calculateLive()">
                    <textarea id="o_${m.id}" class="form-control obs-input" rows="2" placeholder="Note..."></textarea>
                </div>`;
            });
        }

        function toggleObs(id) {
            const el = document.getElementById(`o_${id}`);
            const icon = document.getElementById(`toggle_${id}`);
            el.style.display = el.style.display === 'block' ? 'none' : 'block';
            icon.classList.toggle('active');
            if (el.style.display === 'block') el.focus();
        }

        function initProductList() {
            const dl = document.getElementById('pList');
            dl.innerHTML = '';
            prods.forEach(p => dl.innerHTML += `<option value="${p.name}">`);
        }

        function initRefList() {
            const sel = document.getElementById('inRef');
            sel.innerHTML = `<option value="">Sélectionner ${appMode === 'CO2' ? 'CO2 Type' : 'Préforme'}...</option>`;
            refs.forEach(r => sel.innerHTML += `<option value="${r}">${r}</option>`);
        }

        function calculateLive() {
            const pName = document.getElementById('inProd').value;
            const packs = parseFloat(document.getElementById('inPacks').value) || 0;
            const prd = prods.find(x => x.name === pName);
            const coeff = prd ? prd.coeff : 0;
            const theo = packs * coeff;

            if (appMode === 'CO2') {
                const start = parseFloat(document.getElementById('inStart').value) || 0;
                const end = parseFloat(document.getElementById('inEnd').value) || 0;
                const real = end - start;
                const yieldVal = real > 0 ? (theo / real) * 100 : 0;

                document.getElementById('liveVal1').innerText = real.toFixed(2);
                document.getElementById('liveVal2').innerText = theo.toFixed(2);
                document.getElementById('liveYield').innerText = yieldVal.toFixed(1) + "%";
            } else {
                const real = parseFloat(document.getElementById('inReal').value) || 0;
                let totalLosses = 0;
                machineKeys.forEach(m => {
                    totalLosses += parseFloat(document.getElementById(`p_${m.id}`).value) || 0;
                });

                const ecart = real - theo;
                document.getElementById('liveVal1').innerText = ecart.toFixed(2);
                document.getElementById('liveVal2').innerText = (ecart - totalLosses).toFixed(2);
            }
        }

        function saveReport() {
            const pName = document.getElementById('inProd').value;
            const prd = prods.find(x => x.name === pName);
            const coeff = prd ? prd.coeff : 0;
            const packs = parseFloat(document.getElementById('inPacks').value) || 0;
            const theo = packs * coeff;

            let real = 0;
            let counters = null;

            if (appMode === 'CO2') {
                const start = parseFloat(document.getElementById('inStart').value) || 0;
                const end = parseFloat(document.getElementById('inEnd').value) || 0;
                real = end - start;
                counters = { start, end };
            } else {
                real = parseFloat(document.getElementById('inReal').value) || 0;
            }

            const entry = {
                id: Date.now(),
                mode: appMode,
                date: document.getElementById('inDate').value,
                line: document.getElementById('inLine').value,
                preform: document.getElementById('inRef').value,
                prod: pName,
                packs: packs,
                real: real,
                theo: theo,
                counters: counters,
                losses: {},
                notes: {},
                generalNote: document.getElementById('inGeneralNote').value.trim()
            };
            machineKeys.forEach(m => {
                entry.losses[m.id] = parseInt(document.getElementById(`p_${m.id}`).value) || 0;
                entry.notes[m.id] = document.getElementById(`o_${m.id}`).value.trim();
            });
            entry.totalLoss = Object.values(entry.losses).reduce((a, b) => a + b, 0);
            entry.yieldVal = real > 0 ? ((theo / real) * 100).toFixed(1) : 0;

            if (editingId) {
                const idx = hist.findIndex(h => h.id === editingId);
                if (idx !== -1) hist[idx] = entry;
                editingId = null;
            } else {
                hist.push(entry);
            }
            localStorage.setItem('pet_v17_hist', JSON.stringify(hist));
            location.reload();
        }

        function refreshUI() {
            const line = document.getElementById('fLine').value;
            let filtered = hist.filter(h => h.mode === appMode); // Mode Filter
            if (line !== 'ALL') filtered = filtered.filter(h => h.line === line);

            let sums = {}, totalAll = 0, yieldSum = 0;
            machineKeys.forEach(m => sums[m.id] = 0);
            filtered.forEach(h => {
                totalAll += h.totalLoss;
                yieldSum += parseFloat(h.yieldVal);
                machineKeys.forEach(m => sums[m.id] += h.losses[m.id]);
            });

            document.getElementById('totalLosses').innerText = totalAll.toLocaleString();
            const avgYVal = filtered.length ? (yieldSum / filtered.length) : 0;
            const avgEl = document.getElementById('avgYield');
            avgEl.innerText = avgYVal.toFixed(1) + "%";

            // Average Color coding
            let totalObj = 0;
            filtered.forEach(h => totalObj += (activeData.objectives ? activeData.objectives[h.line] : 0));
            const avgObj = filtered.length ? (totalObj / filtered.length) : 0;
            avgEl.className = (avgYVal >= avgObj && avgYVal > 0) ? 'fw-bold text-success mb-0' : 'fw-bold text-danger mb-0';
            const statsCont = document.getElementById('statsContainer');
            statsCont.innerHTML = '';
            machineKeys.forEach(m => {
                statsCont.innerHTML += `
                <div class="stat-item" onclick="showMachineNotes('${m.id}', '${m.label}')">
                    <span class="stat-label">${m.label}</span>
                    <span class="stat-value">${sums[m.id].toLocaleString()}</span>
                </div>`;
            });

            const tbody = document.getElementById('historyBody');
            tbody.innerHTML = '';
            filtered.slice(-15).reverse().forEach(h => {
                const objective = activeData.objectives ? activeData.objectives[h.line] : 0;
                const isSuccess = parseFloat(h.yieldVal) >= objective;
                const statusClass = isSuccess ? 'text-success' : 'text-danger';
                const noteIcon = h.generalNote ? `<i class="bi bi-info-circle ms-1" title="${h.generalNote}" onclick="alert('Note: ${h.generalNote}')"></i>` : '';

                tbody.innerHTML += `<tr>
                    <td>${h.date.slice(5)}</td>
                    <td class="text-start">${h.prod.slice(0, 12)}</td>
                    <td class="fw-bold ${statusClass}">${h.yieldVal}%${noteIcon}</td>
                    <td class="fw-bold text-danger">${h.totalLoss}</td>
                    <td>
                        <i class="bi bi-pencil-square text-primary me-2 pointer" onclick="editEntry(${h.id})"></i>
                        <i class="bi bi-trash text-danger pointer" onclick="launchAuth('DELETE', ${h.id})"></i>
                    </td>
                </tr>`;
            });

            updateChart(filtered.slice(-15));
        }

        // --- وظيفة عرض جميع الملاحظات ---
        function showAllNotes() {
            const modalBody = document.getElementById('notesModalBody');
            document.getElementById('notesModalTitle').innerHTML = '<i class="bi bi-journal-text me-2"></i> Journal Global des Notes';
            modalBody.innerHTML = '';

            const line = document.getElementById('fLine').value;
            let filtered = line === 'ALL' ? hist : hist.filter(h => h.line === line);

            let notesFound = false;

            // التكرار عبر التقارير من الأحدث للأقدم
            filtered.slice().reverse().forEach(entry => {
                machineKeys.forEach(m => {
                    if (entry.notes[m.id] && entry.notes[m.id] !== "") {
                        notesFound = true;
                        modalBody.innerHTML += `
                        <div class="note-entry shadow-sm">
                            <div class="note-meta">
                                <span><i class="bi bi-calendar3 me-1"></i> ${entry.date} | ${entry.line} | <span class="badge bg-secondary">${entry.preform || '-'}</span></span>
                                <span class="badge bg-danger">${m.label}</span>
                            </div>
                            <div class="fw-bold small mb-1">${entry.prod}</div>
                            <div class="text-dark bg-white p-2 rounded border small">"${entry.notes[m.id]}"</div>
                        </div>`;
                    }
                });
            });

            if (!notesFound) {
                modalBody.innerHTML = '<div class="text-center p-5 text-muted"><i class="bi bi-chat-square-x style="font-size: 3rem;"></i><p class="mt-2">Aucune note enregistrée.</p></div>';
            }

            new bootstrap.Modal(document.getElementById('notesViewModal')).show();
        }

        // عرض ملاحظات آلة محددة
        function showMachineNotes(machineId, machineLabel) {
            const modalBody = document.getElementById('notesModalBody');
            document.getElementById('notesModalTitle').innerText = "Notes: " + machineLabel;
            modalBody.innerHTML = '';

            const line = document.getElementById('fLine').value;
            let filtered = hist.filter(h => h.mode === appMode);
            if (line !== 'ALL') filtered = filtered.filter(h => h.line === line);
            filtered = filtered.slice().reverse();

            let count = 0;
            filtered.forEach(n => {
                if (n.notes[machineId]) {
                    count++;
                    modalBody.innerHTML += `
                    <div class="note-entry">
                        <div class="note-meta"><span>${n.date}</span><span>${n.prod}</span></div>
                        <div class="small">${n.notes[machineId]}</div>
                    </div>`;
                }
            });

            if (count === 0) modalBody.innerHTML = '<p class="text-center p-4">Aucune note.</p>';
            new bootstrap.Modal(document.getElementById('notesViewModal')).show();
        }

        function updateChart(data) {
            const ctx = document.getElementById('yieldChart').getContext('2d');
            if (chart) chart.destroy();
            chart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: data.map(d => d.date.slice(5)),
                    datasets: [{ label: 'Yield %', data: data.map(d => d.yieldVal), borderColor: '#3498db', fill: true, backgroundColor: 'rgba(52,152,219,0.1)' }]
                },
                options: { maintainAspectRatio: false }
            });
        }

        // --- Editing Functions ---
        function editEntry(id) {
            const entry = hist.find(h => h.id === id);
            if (!entry) return;

            editingId = id;
            if (entry.mode !== appMode) switchMode(entry.mode);

            // Populate Form
            document.getElementById('inDate').value = entry.date;
            document.getElementById('inLine').value = entry.line;
            document.getElementById('inProd').value = entry.prod;
            document.getElementById('inPacks').value = entry.packs;
            document.getElementById('inRef').value = entry.preform || "";
            document.getElementById('inGeneralNote').value = entry.generalNote || "";

            if (entry.mode === 'CO2' && entry.counters) {
                document.getElementById('inStart').value = entry.counters.start;
                document.getElementById('inEnd').value = entry.counters.end;
            } else {
                document.getElementById('inReal').value = entry.real;
                machineKeys.forEach(m => {
                    document.getElementById(`p_${m.id}`).value = entry.losses[m.id] || 0;
                    document.getElementById(`o_${m.id}`).value = entry.notes[m.id] || "";
                });
            }

            document.getElementById('btnSave').innerText = "METTRE À JOUR";
            document.getElementById('btnCancelEdit').style.display = 'block';
            calculateLive();
            window.scrollTo({ top: 0, behavior: 'smooth' });
        }

        function cancelEdit() {
            editingId = null;
            document.getElementById('productionForm').reset();
            document.getElementById('inDate').valueAsDate = new Date();
            document.getElementById('btnSave').innerText = "ENREGISTRER";
            document.getElementById('btnCancelEdit').style.display = 'none';
            calculateLive();
        }

        function exportExcel() {
            const filteredHist = hist.filter(h => h.mode === appMode);
            if (filteredHist.length === 0) return alert(`Aucune donnée à exporter pour le mode ${appMode}.`);

            const data = filteredHist.map(h => {
                let row = {
                    Date: h.date,
                    Mode: h.mode,
                    Ligne: h.line,
                    Produit: h.prod,
                    Reference: h.preform || "-",
                    "Compteur Packs": h.packs,
                    "Réel": h.real,
                    "Théorique": h.theo,
                    "Yield %": h.yieldVal + "%",
                    "Total Pertes": h.totalLoss,
                    "Note Générale": h.generalNote || ""
                };

                // Add machine details
                machineKeys.forEach(m => {
                    row[m.label + " Loss"] = h.losses[m.id] || 0;
                    row[m.label + " Note"] = h.notes[m.id] || "";
                });

                return row;
            });

            const ws = XLSX.utils.json_to_sheet(data);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Historique");
            const fileName = `PET_Export_${appMode}_${new Date().toISOString().slice(0, 10)}.xlsx`;
            XLSX.writeFile(wb, fileName);
        }

        function launchAuth(action, data) { pendingAction = action; pendingData = data; new bootstrap.Modal(document.getElementById('authModal')).show(); }
        function confirmAuth() {
            if (document.getElementById('modalPassInput').value === appPass) {
                bootstrap.Modal.getInstance(document.getElementById('authModal')).hide();
                document.getElementById('modalPassInput').value = ""; // Clear pass
                if (pendingAction === 'SAVE') saveReport();
                if (pendingAction === 'DELETE') { hist = hist.filter(h => h.id !== pendingData); localStorage.setItem('pet_v17_hist', JSON.stringify(hist)); refreshUI(); }
                if (pendingAction === 'CONFIG') openSettings();
            } else alert("PIN Erreur");
        }

        // --- Fonctions Paramètres ---
        function openSettings() {
            document.getElementById('setModeTitle').innerText = appMode;
            document.getElementById('tabRefLabel').innerText = appMode === 'CO2' ? 'CO2 Types' : 'Préformes';
            document.getElementById('refTableLabel').innerText = appMode === 'CO2' ? 'CO2 Type' : 'Préforme';

            renderSettingsProducts();
            renderSettingsRefs();
            renderSettingsObjectives();
            document.getElementById('currentPinRef').innerText = appPass;
            new bootstrap.Modal(document.getElementById('settingsModal')).show();
        }

        function renderSettingsObjectives() {
            const tbody = document.getElementById('settingsObjBody');
            tbody.innerHTML = '';
            const lines = appMode === 'CO2' ? ['CSD', 'CAN', 'RGB'] : ['CSD', 'WATER'];
            lines.forEach(line => {
                const val = (activeData.objectives && activeData.objectives[line]) || 0;
                tbody.innerHTML += `
                <tr>
                    <td>Ligne ${line}</td>
                    <td><input type="number" id="obj_${line}" class="form-control form-control-sm" value="${val}"></td>
                </tr>`;
            });
        }

        function saveObjectives() {
            const lines = appMode === 'CO2' ? ['CSD', 'CAN', 'RGB'] : ['CSD', 'WATER'];
            if (!activeData.objectives) activeData.objectives = {};
            lines.forEach(line => {
                activeData.objectives[line] = parseFloat(document.getElementById(`obj_${line}`).value) || 0;
            });
            saveData();
            alert("Objectifs enregistrés !");
        }

        function renderSettingsProducts() {
            const tbody = document.getElementById('settingsProductBody');
            tbody.innerHTML = '';
            prods.forEach((p, idx) => {
                tbody.innerHTML += `
                <tr>
                    <td><input type="text" class="form-control form-control-sm" value="${p.name}" onchange="editProduct(${idx}, 'name', this.value)"></td>
                    <td><input type="number" step="0.001" class="form-control form-control-sm" value="${p.coeff}" onchange="editProduct(${idx}, 'coeff', this.value)"></td>
                    <td><button class="btn btn-sm btn-danger" onclick="deleteProduct(${idx})"><i class="bi bi-trash"></i></button></td>
                </tr>`;
            });
        }

        function addProduct() {
            const name = document.getElementById('newProdName').value.trim();
            const coeff = parseFloat(document.getElementById('newProdCoeff').value) || 0;
            if (name && coeff > 0) {
                prods.push({ name, coeff });
                saveData();
                document.getElementById('newProdName').value = "";
                document.getElementById('newProdCoeff').value = "";
                renderSettingsProducts();
                initProductList();
            }
        }

        function editProduct(idx, field, val) {
            if (field === 'coeff') prods[idx].coeff = parseFloat(val) || 0;
            else prods[idx].name = val;
            saveData();
            initProductList();
        }

        function deleteProduct(idx) {
            if (confirm("Supprimer ce produit ?")) {
                prods.splice(idx, 1);
                saveData();
                renderSettingsProducts();
                initProductList();
            }
        }

        function saveData() {
            dataSets[appMode].prods = prods;
            dataSets[appMode].refs = refs;
            localStorage.setItem('pet_v17_datasets', JSON.stringify(dataSets));
        }

        function renderSettingsRefs() {
            const tbody = document.getElementById('settingsRefBody');
            tbody.innerHTML = '';
            refs.forEach((r, idx) => {
                tbody.innerHTML += `
                <tr>
                    <td><input type="text" class="form-control form-control-sm" value="${r}" onchange="editRef(${idx}, this.value)"></td>
                    <td><button class="btn btn-sm btn-danger" onclick="deleteRef(${idx})"><i class="bi bi-trash"></i></button></td>
                </tr>`;
            });
        }

        function addRef() {
            const name = document.getElementById('newRefName').value.trim();
            if (name) {
                refs.push(name);
                saveData();
                document.getElementById('newRefName').value = "";
                renderSettingsRefs();
                initRefList();
            }
        }

        function editRef(idx, val) {
            refs[idx] = val;
            saveData();
            initRefList();
        }

        function deleteRef(idx) {
            if (confirm("Supprimer cette référence ?")) {
                refs.splice(idx, 1);
                saveData();
                renderSettingsRefs();
                initRefList();
            }
        }

        function changePIN() {
            const newPin = document.getElementById('newPinInput').value.trim();
            if (newPin.length >= 4) {
                appPass = newPin;
                localStorage.setItem('pet_v17_pwd', appPass);
                document.getElementById('currentPinRef').innerText = appPass;
                document.getElementById('newPinInput').value = "";
                alert("Code PIN mis à jour avec succès !");
            } else alert("Le PIN doit avoir au moins 4 chiffres.");
        }

        function clearHistory() {
            if (confirm("⚠️ Êtes-vous sûr de vouloir supprimer TOUT l'historique ? Cette action est irréversible.")) {
                hist = [];
                localStorage.setItem('pet_v17_hist', JSON.stringify(hist));
                location.reload();
            }
        }

        function exportData() {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(hist, null, 2));
            const downloadAnchorNode = document.createElement('a');
            downloadAnchorNode.setAttribute("href", dataStr);
            downloadAnchorNode.setAttribute("download", "pet_dashboard_backup_" + new Date().toISOString().slice(0, 10) + ".json");
            document.body.appendChild(downloadAnchorNode);
            downloadAnchorNode.click();
            downloadAnchorNode.remove();
        }
    </script>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>

</html>
