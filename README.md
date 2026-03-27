<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Genetics Simulation - With Environmental Pressure</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Lexend:wght@300;400;700&display=swap');
        body { font-family: 'Lexend', sans-serif; }
        .card { 
            display: inline-flex;
            align-items: center;
            justify-content: center;
            width: 60px;
            height: 80px;
            margin: 5px;
            border: 2px solid #333;
            border-radius: 8px;
            font-weight: bold;
            font-size: 24px;
            transition: all 0.3s;
        }
        .card.L { background-color: #3b82f6; color: white; }
        .card.l { background-color: #ef4444; color: white; }
        .genotype-display {
            font-size: 32px;
            font-weight: bold;
            padding: 15px 25px;
            border-radius: 8px;
            text-align: center;
            min-width: 120px;
        }
        .genotype-LL { background-color: #dbeafe; color: #1e40af; }
        .genotype-Ll { background-color: #fef3c7; color: #92400e; }
        .genotype-ll { background-color: #fee2e2; color: #7f1d1d; }
        .eliminated-badge {
            background-color: #dc2626;
            color: white;
            padding: 8px 12px;
            border-radius: 6px;
            font-size: 12px;
            font-weight: bold;
            margin-top: 8px;
        }
    </style>
</head>
<body class="bg-slate-100 min-h-screen pb-12">
    <div class="max-w-6xl mx-auto p-4">
        <!-- Header -->
        <header class="bg-white rounded-2xl shadow-sm p-6 mb-6 mt-4 border-b-4 border-red-500">
            <div>
                <h1 class="text-3xl font-bold text-slate-800">🧬 Genetics Simulator - Environmental Pressure</h1>
                <p class="text-slate-500">Track your genotype across 5 generations. <span class="font-bold text-red-600">⚠️ ll genotypes cannot reproduce!</span></p>
            </div>
        </header>

        <!-- Environmental Pressure Alert -->
        <div class="bg-red-50 border-l-4 border-red-500 p-4 mb-6 rounded-lg">
            <p class="text-red-800 font-bold"><i class="fas fa-exclamation-triangle"></i> Environmental Pressure Active</p>
            <p class="text-red-700 text-sm mt-1">Individuals with ll genotype cannot survive to reproduce. This simulates natural selection.</p>
        </div>

        <!-- Trial Selection -->
        <div class="bg-white rounded-2xl shadow-sm p-6 mb-6 border border-slate-200">
            <h2 class="font-bold text-xl mb-4">Select Trial</h2>
            <div class="flex gap-4">
                <button onclick="setTrial(1)" id="trial-1" class="px-6 py-3 rounded-lg border-2 border-blue-500 bg-blue-50 text-blue-700 font-bold">Trial 1</button>
                <button onclick="setTrial(2)" id="trial-2" class="px-6 py-3 rounded-lg border-2 border-slate-200 text-slate-600 font-bold hover:border-blue-300">Trial 2</button>
                <button onclick="setTrial(3)" id="trial-3" class="px-6 py-3 rounded-lg border-2 border-slate-200 text-slate-600 font-bold hover:border-blue-300">Trial 3</button>
            </div>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
            <!-- Left Panel -->
            <div class="space-y-4">
                <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200">
                    <h3 class="font-bold text-lg mb-4">Your Status</h3>
                    <div class="space-y-3">
                        <div>
                            <p class="text-sm text-slate-500 uppercase">Generation</p>
                            <p id="gen-display" class="text-3xl font-bold text-blue-600">1</p>
                        </div>
                        <div>
                            <p class="text-sm text-slate-500 uppercase">Your Genotype</p>
                            <div id="current-genotype" class="genotype-display genotype-Ll">Ll</div>
                        </div>
                        <div id="alive-status" class="hidden">
                            <p class="text-sm text-slate-500 uppercase">Status</p>
                            <p class="text-lg font-bold text-green-600"><i class="fas fa-heart"></i> Alive & Can Reproduce</p>
                        </div>
                        <div id="dead-status" class="hidden">
                            <p class="text-sm text-slate-500 uppercase">Status</p>
                            <p class="text-lg font-bold text-red-600"><i class="fas fa-skull"></i> Eliminated by Pressure</p>
                        </div>
                    </div>
                </div>

                <div class="bg-red-50 p-6 rounded-2xl border-l-4 border-red-500">
                    <h3 class="font-bold text-red-900 mb-2">📋 How It Works</h3>
                    <ol class="text-sm text-red-800 space-y-2 list-decimal list-inside">
                        <li>Click "Find Partner"</li>
                        <li>See offspring genotype</li>
                        <li>If you get ll, you're eliminated</li>
                        <li>Your new genotype becomes your starting point</li>
                        <li>Repeat 5 generations</li>
                    </ol>
                </div>

                <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 space-y-3">
                    <button onclick="findPartner()" id="btn-partner" class="w-full bg-green-600 hover:bg-green-700 text-white font-bold py-3 rounded-lg">
                        <i class="fas fa-handshake"></i> Find Partner
                    </button>
                    <button onclick="resetAllTrials()" class="w-full bg-red-500 hover:bg-red-600 text-white font-bold py-2 rounded-lg text-sm">
                        <i class="fas fa-redo"></i> Reset All
                    </button>
                </div>
            </div>

            <!-- Middle Panel -->
            <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200">
                <h3 class="font-bold text-lg mb-4">Generation Result</h3>
                
                <div id="result-section" class="hidden space-y-4 text-center">
                    <div class="p-4 bg-blue-50 border border-blue-300 rounded-lg">
                        <p class="text-sm text-blue-600 font-bold mb-2">Your Genotype</p>
                        <p id="your-geno" class="text-2xl font-bold text-blue-700">Ll</p>
                    </div>

                    <div class="text-2xl">+</div>

                    <div class="p-4 bg-green-50 border border-green-300 rounded-lg">
                        <p class="text-sm text-green-600 font-bold mb-2">Partner Genotype</p>
                        <p id="partner-geno" class="text-2xl font-bold text-green-700">Ll</p>
                    </div>

                    <div class="border-t-2 border-slate-300 pt-4 mt-4">
                        <p class="text-sm text-slate-500 mb-2">Offspring Genotype</p>
                        <p id="offspring-geno" class="text-4xl font-bold text-purple-600">Ll</p>
                    </div>

                    <!-- Environmental Pressure Warning -->
                    <div id="pressure-warning" class="hidden bg-red-100 border border-red-400 rounded-lg p-4">
                        <p class="text-red-900 font-bold"><i class="fas fa-skull-crossbones"></i> Eliminated!</p>
                        <p class="text-red-800 text-sm mt-2">Your ll genotype cannot survive environmental pressure.</p>
                        <p class="text-red-800 text-sm mt-1">You cannot contribute alleles to the next generation.</p>
                        <button onclick="continueAfterElimination()" class="mt-3 px-4 py-2 bg-red-600 hover:bg-red-700 text-white rounded font-bold text-sm">
                            Continue to Next Generation
                        </button>
                    </div>

                    <!-- Success Message -->
                    <div id="success-message" class="hidden bg-green-100 border border-green-400 rounded-lg p-4">
                        <p class="text-green-900 font-bold"><i class="fas fa-check-circle"></i> Survived!</p>
                        <p class="text-green-800 text-sm mt-2">You can reproduce and contribute alleles to the next generation.</p>
                    </div>
                </div>

                <div id="no-result" class="text-slate-400 text-center py-12">
                    Click "Find Partner" to begin
                </div>
            </div>

            <!-- Right Panel: History -->
            <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200">
                <h3 class="font-bold text-lg mb-4">📊 Your Genotypes (Trial <span id="trial-num">1</span>)</h3>
                <div class="overflow-x-auto">
                    <table class="w-full text-sm border-collapse">
                        <thead>
                            <tr class="bg-slate-100 border-b font-bold">
                                <th class="p-2 text-left">Gen</th>
                                <th class="p-2 text-center">Genotype</th>
                                <th class="p-2 text-center">Status</th>
                            </tr>
                        </thead>
                        <tbody id="history-body">
                            <tr class="border-b">
                                <td class="p-2 font-bold">1</td>
                                <td class="p-2 text-center font-bold">Ll</td>
                                <td class="p-2 text-center text-green-600 font-bold"><i class="fas fa-heart"></i></td>
                            </tr>
                        </tbody>
                    </table>
                </div>
                <div class="mt-4 pt-4 border-t space-y-2">
                    <button onclick="downloadResults()" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 rounded-lg text-sm">
                        <i class="fas fa-download"></i> Download
                    </button>
                    <button onclick="viewAllTrials()" class="w-full bg-slate-600 hover:bg-slate-700 text-white font-bold py-2 rounded-lg text-sm">
                        <i class="fas fa-table"></i> All Trials
                    </button>
                </div>
            </div>
        </div>

        <!-- Completion Message -->
        <div id="completion" class="hidden mt-6 bg-green-50 border border-green-300 rounded-2xl p-6 text-center">
            <h3 class="font-bold text-green-900 text-xl">✓ Trial Complete!</h3>
            <p class="text-green-800 mt-2">You completed 5 generations. View results above.</p>
        </div>

        <!-- All Trials Modal -->
        <div id="modal" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
            <div class="bg-white rounded-2xl p-8 max-w-4xl max-h-96 overflow-y-auto">
                <h2 class="font-bold text-2xl mb-4">All Trials Results</h2>
                <div id="modal-content" class="grid grid-cols-3 gap-4"></div>
                <button onclick="document.getElementById('modal').classList.add('hidden')" class="mt-4 w-full px-6 py-2 bg-slate-600 hover:bg-slate-700 text-white rounded-lg">Close</button>
            </div>
        </div>
    </div>

    <script>
        let currentTrial = 1;
        let currentGeneration = 1;
        let currentGenotype = 'Ll';
        let isAlive = true;
        let pendingPartnerGenotype = null;

        let trials = {
            1: { data: [{ gen: 'Ll', status: 'alive' }] },
            2: { data: [{ gen: 'Ll', status: 'alive' }] },
            3: { data: [{ gen: 'Ll', status: 'alive' }] }
        };

        function getRandomGenotype() {
            const types = ['LL', 'Ll', 'll'];
            return types[Math.floor(Math.random() * types.length)];
        }

        function setTrial(trial) {
            currentTrial = trial;
            currentGeneration = 1;
            currentGenotype = 'Ll';
            isAlive = true;

            for (let t = 1; t <= 3; t++) {
                const btn = document.getElementById(`trial-${t}`);
                if (t === trial) {
                    btn.className = 'px-6 py-3 rounded-lg border-2 border-blue-500 bg-blue-50 text-blue-700 font-bold';
                } else {
                    btn.className = 'px-6 py-3 rounded-lg border-2 border-slate-200 text-slate-600 font-bold hover:border-blue-300';
                }
            }

            document.getElementById('trial-num').textContent = trial;
            document.getElementById('completion').classList.add('hidden');
            document.getElementById('result-section').classList.add('hidden');
            document.getElementById('no-result').classList.remove('hidden');
            document.getElementById('alive-status').classList.add('hidden');
            document.getElementById('dead-status').classList.add('hidden');
            updateDisplay();
        }

        function findPartner() {
            if (currentGeneration > 5) {
                alert('Trial complete! Switch trials or reset to start over.');
                return;
            }

            if (!isAlive) {
                alert('You were eliminated! You cannot reproduce. Switch trials or reset.');
                return;
            }

            const partnerGenotype = getRandomGenotype();
            
            // Get random alleles from current and partner genotypes
            const currentAlleles = currentGenotype.split('');
            const partnerAlleles = partnerGenotype.split('');
            
            const yourAllele = currentAlleles[Math.floor(Math.random() * currentAlleles.length)];
            const partnerAllele = partnerAlleles[Math.floor(Math.random() * partnerAlleles.length)];
            
            const offspring = [yourAllele, partnerAllele].sort().join('');

            // Check environmental pressure
            if (offspring === 'll') {
                // ll genotype cannot survive
                pendingPartnerGenotype = partnerGenotype;
                currentGeneration++;
                
                // Record but mark as eliminated
                trials[currentTrial].data.push({ gen: offspring, status: 'eliminated' });
                
                isAlive = false;

                // Display elimination
                let previousGeno = trials[currentTrial].data[trials[currentTrial].data.length - 2].gen;
                document.getElementById('your-geno').textContent = previousGeno;
                document.getElementById('partner-geno').textContent = partnerGenotype;
                document.getElementById('offspring-geno').textContent = offspring;
                document.getElementById('success-message').classList.add('hidden');
                document.getElementById('pressure-warning').classList.remove('hidden');

                document.getElementById('no-result').classList.add('hidden');
                document.getElementById('result-section').classList.remove('hidden');

                updateDisplay();
            } else {
                // Survived - can reproduce
                currentGenotype = offspring;
                currentGeneration++;
                trials[currentTrial].data.push({ gen: offspring, status: 'alive' });

                // Display success
                let previousGeno = trials[currentTrial].data[trials[currentTrial].data.length - 2].gen;
                document.getElementById('your-geno').textContent = previousGeno;
                document.getElementById('partner-geno').textContent = partnerGenotype;
                document.getElementById('offspring-geno').textContent = offspring;
                document.getElementById('pressure-warning').classList.add('hidden');
                document.getElementById('success-message').classList.remove('hidden');

                document.getElementById('no-result').classList.add('hidden');
                document.getElementById('result-section').classList.remove('hidden');

                updateDisplay();

                if (currentGeneration > 5) {
                    setTimeout(() => {
                        document.getElementById('completion').classList.remove('hidden');
                    }, 500);
                }
            }
        }

        function continueAfterElimination() {
            if (currentGeneration > 5) {
                document.getElementById('completion').classList.remove('hidden');
            } else {
                document.getElementById('result-section').classList.add('hidden');
                document.getElementById('no-result').classList.remove('hidden');
            }
        }

        function updateDisplay() {
            document.getElementById('gen-display').textContent = currentGeneration;
            
            const genotypeEl = document.getElementById('current-genotype');
            genotypeEl.textContent = currentGenotype;
            genotypeEl.className = 'genotype-display genotype-' + currentGenotype;

            // Show status
            if (isAlive) {
                document.getElementById('alive-status').classList.remove('hidden');
                document.getElementById('dead-status').classList.add('hidden');
            } else {
                document.getElementById('alive-status').classList.add('hidden');
                document.getElementById('dead-status').classList.remove('hidden');
            }

            // Update history
            const tbody = document.getElementById('history-body');
            tbody.innerHTML = trials[currentTrial].data.map((entry, idx) => {
                const statusIcon = entry.status === 'alive' ? '<i class="fas fa-heart text-green-600"></i>' : '<i class="fas fa-skull text-red-600"></i>';
                return `
                    <tr class="border-b">
                        <td class="p-2 font-bold">${idx + 1}</td>
                        <td class="p-2 text-center font-bold">${entry.gen}</td>
                        <td class="p-2 text-center">${statusIcon}</td>
                    </tr>
                `;
            }).join('');
        }

        function resetAllTrials() {
            // Reset all trials
            trials = {
                1: { data: [{ gen: 'Ll', status: 'alive' }] },
                2: { data: [{ gen: 'Ll', status: 'alive' }] },
                3: { data: [{ gen: 'Ll', status: 'alive' }] }
            };

            // Reset current trial
            currentTrial = 1;
            currentGeneration = 1;
            currentGenotype = 'Ll';
            isAlive = true;

            // Reset UI - Trial 1 button
            for (let t = 1; t <= 3; t++) {
                const btn = document.getElementById(`trial-${t}`);
                if (t === 1) {
                    btn.className = 'px-6 py-3 rounded-lg border-2 border-blue-500 bg-blue-50 text-blue-700 font-bold';
                } else {
                    btn.className = 'px-6 py-3 rounded-lg border-2 border-slate-200 text-slate-600 font-bold hover:border-blue-300';
                }
            }

            document.getElementById('trial-num').textContent = '1';
            document.getElementById('completion').classList.add('hidden');
            document.getElementById('result-section').classList.add('hidden');
            document.getElementById('no-result').classList.remove('hidden');
            document.getElementById('alive-status').classList.add('hidden');
            document.getElementById('dead-status').classList.add('hidden');
            
            // Update all displays
            updateDisplay();
            
            alert('✓ All trials have been reset to generation 1!');
        }

        function downloadResults() {
            const data = trials[currentTrial].data;
            let csv = `Trial ${currentTrial}\nGeneration,Genotype,Status\n`;
            data.forEach((entry, idx) => {
                csv += `${idx + 1},${entry.gen},${entry.status}\n`;
            });

            const blob = new Blob([csv], { type: 'text/csv' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `Trial_${currentTrial}_Environmental_Pressure.csv`;
            a.click();
        }

        function viewAllTrials() {
            let html = '';
            for (let t = 1; t <= 3; t++) {
                html += `
                    <div class="border rounded-lg p-4">
                        <h3 class="font-bold mb-2">Trial ${t}</h3>
                        <table class="text-sm w-full">
                            <thead class="bg-gray-100"><tr><th class="p-1">Gen</th><th class="p-1">Genotype</th><th class="p-1">Status</th></tr></thead>
                            <tbody>
                                ${trials[t].data.map((entry, idx) => {
                                    const statusIcon = entry.status === 'alive' ? '✓' : '✗';
                                    return `<tr class="border-t"><td class="p-1">${idx + 1}</td><td class="p-1 font-bold">${entry.gen}</td><td class="p-1 text-center">${statusIcon}</td></tr>`;
                                }).join('')}
                            </tbody>
                        </table>
                    </div>
                `;
            }
            document.getElementById('modal-content').innerHTML = html;
            document.getElementById('modal').classList.remove('hidden');
        }

        setTrial(1);
    </script>
</body>
</html>
