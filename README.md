<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TI NOVA POS | Premium Enterprise Hub</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; 
            background: radial-gradient(at top left, #f8faff, #e0e7ff);
            min-height: 100vh;
            color: #1e293b;
        }

        .glass-panel {
            background: rgba(255, 255, 255, 0.7);
            backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.5);
            box-shadow: 0 10px 40px -10px rgba(0, 0, 0, 0.05);
        }

        .grad-income { background: linear-gradient(135deg, #10b981 0%, #059669 100%); }
        .grad-due { background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%); }
        .grad-complete { background: linear-gradient(135deg, #6366f1 0%, #4f46e5 100%); }
        .grad-pending { background: linear-gradient(135deg, #f43f5e 0%, #e11d48 100%); }
        .grad-sidebar { background: linear-gradient(180deg, #0f172a 0%, #1e293b 100%); }

        .stat-card { transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        .stat-card:hover { transform: translateY(-8px); }

        @media print { 
            .no-print { display: none !important; }
            body { background: white !important; }
            .glass-panel { border: none !important; box-shadow: none !important; }
            #invoiceModal { position: absolute; left: 0; top: 0; background: white !important; width: 100%; height: 100%; display: block !important; }
            .modal-content { box-shadow: none !important; border: none !important; width: 100% !important; max-width: 100% !important; }
        }
    </style>
</head>
<body class="flex">

    <aside class="w-72 fixed h-full grad-sidebar text-white hidden lg:flex flex-col p-8 z-50 no-print">
        <div class="mb-12">
            <h1 class="text-3xl font-extrabold tracking-tighter italic">TI NOVA <span class="text-indigo-400">POS</span></h1>
            <p class="text-[10px] font-bold uppercase tracking-[0.3em] text-slate-500 mt-2">Powered by Trexivo IT</p>
        </div>
        <nav class="space-y-3 flex-1">
            <a href="#" class="flex items-center gap-4 p-4 bg-indigo-500/20 border border-indigo-500/30 rounded-2xl text-white font-bold shadow-lg">
                <i class="fas fa-grid-2"></i> Dashboard
            </a>
            <a href="#" class="flex items-center gap-4 p-4 text-slate-400 hover:bg-white/5 rounded-2xl transition">
                <i class="fas fa-users"></i> Clients
            </a>
            <a href="#" class="flex items-center gap-4 p-4 text-slate-400 hover:bg-white/5 rounded-2xl transition">
                <i class="fas fa-file-invoice"></i> Billing
            </a>
        </nav>
        <div class="mt-auto pt-6 border-t border-white/10 text-center">
            <p class="text-[10px] text-slate-500 font-bold tracking-widest uppercase italic">Master Build v6.1</p>
        </div>
    </aside>

    <main class="lg:ml-72 flex-1 p-6 md:p-12">
        
        <header class="flex flex-col md:flex-row justify-between items-center gap-6 mb-12 no-print">
            <div>
                <h1 class="text-4xl font-black text-slate-800 tracking-tighter italic uppercase">Welcome to TI NOVA POS</h1>
                <p class="text-slate-500 text-sm font-semibold italic">Intelligence at your fingertips • <span class="text-indigo-600 font-bold">Trexivo IT</span></p>
            </div>
            
            <div class="flex items-center gap-4 w-full md:w-auto">
                <div class="relative flex-1 md:w-80">
                    <i class="fas fa-search absolute left-5 top-1/2 -translate-y-1/2 text-slate-400"></i>
                    <input type="text" id="searchInput" onkeyup="searchOrders()" placeholder="Quick Search Ledger..." 
                           class="w-full pl-14 pr-6 py-4 glass-panel rounded-3xl border-none outline-none focus:ring-2 ring-indigo-400 font-medium">
                </div>
                <button onclick="openModal()" class="grad-complete text-white w-14 h-14 rounded-2xl flex items-center justify-center text-xl shadow-xl active:scale-95 transition-transform">
                    <i class="fas fa-plus"></i>
                </button>
            </div>
        </header>

        <div class="grid grid-cols-1 md:grid-cols-4 gap-6 mb-12 no-print">
            <div class="stat-card grad-income p-7 rounded-[2.5rem] text-white shadow-2xl relative overflow-hidden">
                <p class="text-white/70 text-[10px] font-black uppercase tracking-widest">Total Income</p>
                <h3 class="text-3xl font-black mt-2 tracking-tighter">$<span id="statIncome">0</span></h3>
            </div>
            <div class="stat-card grad-due p-7 rounded-[2.5rem] text-white shadow-2xl relative overflow-hidden">
                <p class="text-white/70 text-[10px] font-black uppercase tracking-widest">Due Amount</p>
                <h3 class="text-3xl font-black mt-2 tracking-tighter">$<span id="statDue">0</span></h3>
            </div>
            <div class="stat-card grad-complete p-7 rounded-[2.5rem] text-white shadow-2xl relative overflow-hidden">
                <p class="text-white/70 text-[10px] font-black uppercase tracking-widest">Order Success</p>
                <h3 class="text-3xl font-black mt-2 tracking-tighter" id="statConfirmed">0</h3>
            </div>
            <div class="stat-card grad-pending p-7 rounded-[2.5rem] text-white shadow-2xl relative overflow-hidden">
                <p class="text-white/70 text-[10px] font-black uppercase tracking-widest">Order Pending</p>
                <h3 class="text-3xl font-black mt-2 tracking-tighter" id="statPending">0</h3>
            </div>
        </div>

        <div class="glass-panel rounded-[3rem] p-10 mb-12 no-print">
            <h3 class="text-sm font-black text-slate-400 mb-6 uppercase tracking-widest italic">Performance Trend (1 Week)</h3>
            <canvas id="salesChart" height="80"></canvas>
        </div>

        <div class="glass-panel rounded-[3rem] overflow-hidden no-print">
            <div class="overflow-x-auto">
                <table class="w-full text-left">
                    <thead class="bg-slate-50/50 text-slate-400 text-[10px] font-black uppercase tracking-widest">
                        <tr>
                            <th class="px-10 py-6">Inv Code</th>
                            <th class="px-10 py-6">Client</th>
                            <th class="px-10 py-6">Total Bill</th>
                            <th class="px-10 py-6">Payment</th>
                            <th class="px-10 py-6 text-center">Status</th>
                            <th class="px-10 py-6 text-right">Actions</th>
                        </tr>
                    </thead>
                    <tbody id="dataTable" class="divide-y divide-slate-100/50"></tbody>
                </table>
            </div>
        </div>
    </main>

    <div id="modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-xl hidden flex items-center justify-center z-[100] p-4 no-print">
        <div class="bg-white/95 w-full max-w-lg rounded-[3.5rem] p-12 shadow-2xl">
            <h2 class="text-3xl font-black text-slate-800 mb-8 tracking-tighter">New TI NOVA Entry</h2>
            <div class="grid grid-cols-2 gap-5">
                <input type="text" id="clName" placeholder="Client Name" class="col-span-2 p-5 bg-slate-100 border-none rounded-2xl outline-none focus:ring-2 ring-indigo-400">
                <input type="text" id="clPhone" placeholder="Phone" class="p-5 bg-slate-100 border-none rounded-2xl outline-none focus:ring-2 ring-indigo-400">
                <input type="text" id="clService" placeholder="Service" class="p-5 bg-slate-100 border-none rounded-2xl outline-none focus:ring-2 ring-indigo-400">
                <input type="number" id="clPaid" placeholder="Paid Amount" class="p-5 bg-slate-100 border-none rounded-2xl outline-none focus:ring-2 ring-indigo-400">
                <input type="number" id="clDue" placeholder="Initial Due" class="p-5 bg-slate-100 border-none rounded-2xl outline-none focus:ring-2 ring-indigo-400">
            </div>
            <div class="flex gap-4 mt-10">
                <button onclick="closeModal()" class="flex-1 py-5 text-slate-400 font-bold">Discard</button>
                <button onclick="saveOrder()" class="flex-1 py-5 grad-complete text-white font-bold rounded-2xl shadow-xl active:scale-95 transition-transform">Confirm Order</button>
            </div>
        </div>
    </div>

    <div id="invoiceModal" class="fixed inset-0 bg-slate-900/80 backdrop-blur-2xl hidden flex items-center justify-center z-[110] p-4">
        <div class="bg-white w-full max-w-2xl rounded-[3rem] shadow-2xl overflow-hidden border border-white/20 modal-content">
            
            <div class="grad-complete p-10 text-white flex justify-between items-end no-print relative overflow-hidden">
                <div class="relative z-10">
                    <h2 class="text-4xl font-extrabold uppercase italic tracking-tight relative">Official Receipt</h2>
                    <p class="text-[10px] font-bold opacity-80 mt-2 tracking-[0.3em]" id="invD"></p>
                </div>
                <div class="text-right relative z-10">
                    <p class="text-2xl font-black italic tracking-tighter">TI NOVA <span class="text-indigo-200">POS</span></p>
                </div>
            </div>

            <div class="p-12 bg-white relative" id="printArea">
                <div class="flex justify-between items-start mb-12 relative z-10">
                    <div>
                        <p class="text-[10px] font-black text-slate-400 uppercase tracking-[0.2em] mb-2">Billed To</p>
                        <h4 class="text-3xl font-black text-slate-800 tracking-tight" id="invN"></h4>
                        <p id="invP" class="text-slate-500 font-semibold text-sm mt-1"></p>
                    </div>
                    <div class="text-right">
                        <p class="text-[10px] font-black text-slate-400 uppercase tracking-[0.2em] mb-2">Invoice Number</p>
                        <h4 class="text-xl font-black text-indigo-600 tracking-widest" id="invI"></h4>
                    </div>
                </div>

                <div class="border-y-2 border-slate-50 py-8 mb-10 relative z-10">
                    <div class="flex justify-between items-center px-4">
                        <div>
                            <p class="text-[10px] font-black text-indigo-500 uppercase tracking-widest mb-1">Description</p>
                            <span id="invS" class="font-bold text-slate-700 text-lg uppercase tracking-tight"></span>
                        </div>
                        <div class="text-right">
                            <p class="text-[10px] font-black text-slate-400 uppercase tracking-widest mb-1">Amount</p>
                            <span id="invT" class="font-black text-3xl text-slate-900"></span>
                        </div>
                    </div>
                </div>

                <div class="flex justify-end relative z-10">
                    <div class="w-72 space-y-4">
                        <div class="flex justify-between items-center px-4 py-3 bg-rose-50 rounded-xl">
                            <span class="text-[11px] font-black text-rose-500 uppercase">Outstanding Due</span>
                            <span class="font-black text-rose-600" id="invDue"></span>
                        </div>
                        <div class="flex justify-between items-center px-4 pt-6 border-t-4 border-double border-indigo-100">
                            <span class="text-sm font-black text-slate-800 uppercase italic">Grand Total</span>
                            <span class="text-4xl font-black text-indigo-600 tracking-tighter" id="invG"></span>
                        </div>
                    </div>
                </div>
            </div>

            <div class="p-8 bg-slate-50 flex justify-between items-center no-print border-t border-slate-100">
                <button onclick="closeInvoice()" class="px-8 py-4 font-bold text-slate-600 bg-white border border-slate-200 rounded-2xl shadow-sm hover:bg-slate-50 transition-all flex items-center gap-2">
                    <i class="fas fa-arrow-left"></i> BACK TO HUB
                </button>
                <button onclick="window.print()" class="bg-slate-900 text-white px-12 py-4 rounded-2xl font-black shadow-2xl flex items-center gap-3 hover:scale-105 active:scale-95 transition-all">
                    <i class="fas fa-print"></i> PRINT NOW
                </button>
            </div>
        </div>
    </div>

    <script>
        let orders = [];
        
        const salesChart = new Chart(document.getElementById('salesChart'), {
            type: 'line',
            data: { labels: ['M', 'T', 'W', 'T', 'F', 'S', 'S'], datasets: [{ data: [15, 25, 20, 35, 30, 45, 40], borderColor: '#6366f1', fill: true, backgroundColor: 'rgba(99, 102, 241, 0.05)', tension: 0.4 }] },
            options: { plugins: { legend: { display: false } }, scales: { y: { display: false }, x: { grid: { display: false } } } }
        });

        function openModal() { document.getElementById('modal').classList.remove('hidden'); }
        function closeModal() { document.getElementById('modal').classList.add('hidden'); }
        function closeInvoice() { document.getElementById('invoiceModal').classList.add('hidden'); }

        function saveOrder() {
            const name = document.getElementById('clName').value;
            const paid = parseFloat(document.getElementById('clPaid').value) || 0;
            const due = parseFloat(document.getElementById('clDue').value) || 0;
            if(!name) return alert("Please fill Name");
            
            orders.unshift({
                inv: 'NV-' + Math.floor(1000 + Math.random() * 9000),
                name, paid, due, total: paid + due,
                phone: document.getElementById('clPhone').value || 'N/A',
                service: document.getElementById('clService').value || 'Service',
                workStatus: 'Pending',
                date: new Date().toLocaleDateString('en-GB')
            });
            renderTable(); closeModal();
            document.querySelectorAll('#modal input').forEach(i => i.value = '');
        }

        function renderTable(data = orders) {
            const tbody = document.getElementById('dataTable'); tbody.innerHTML = '';
            let income = 0, dueTotal = 0, successCount = 0, pendingCount = 0;
            
            data.forEach((o, i) => {
                if(o.workStatus !== 'Cancelled') {
                    income += o.paid;
                    dueTotal += o.due;
                    if(o.workStatus === 'Success') successCount++;
                    if(o.workStatus === 'Pending' || o.workStatus === 'Confirmed') pendingCount++;
                }
                
                let statusClass = "bg-rose-500";
                if(o.workStatus === 'Confirmed') statusClass = "bg-indigo-600";
                if(o.workStatus === 'Success') statusClass = "bg-emerald-500 shadow-lg";
                if(o.workStatus === 'Cancelled') statusClass = "bg-slate-400 opacity-50";

                tbody.innerHTML += `
                    <tr class="hover:bg-white/50 transition-colors">
                        <td class="px-10 py-6 font-bold text-indigo-600">${o.inv}</td>
                        <td class="px-10 py-6 font-bold text-slate-800">${o.name}</td>
                        <td class="px-10 py-6 font-black">$${o.total.toLocaleString()}</td>
                        <td class="px-10 py-6">
                            <button onclick="settleDue(${i})" class="px-4 py-1.5 rounded-full text-[9px] font-black uppercase ${o.due > 0 && o.workStatus !== 'Cancelled' ? 'bg-rose-100 text-rose-500' : 'bg-emerald-100 text-emerald-600'}">
                                ${o.due > 0 && o.workStatus !== 'Cancelled' ? 'Pay: $'+o.due : 'Settled'}
                            </button>
                        </td>
                        <td class="px-10 py-6 text-center">
                            <button onclick="toggleWork(${i})" class="px-5 py-2 rounded-2xl text-[10px] font-black uppercase text-white ${statusClass}">
                                ${o.workStatus}
                            </button>
                        </td>
                        <td class="px-10 py-6 text-right flex justify-end gap-4">
                            <button onclick="viewInvoice(${i})" class="text-indigo-400 hover:text-indigo-600"><i class="fas fa-file-invoice text-lg"></i></button>
                            <button onclick="cancelOrder(${i})" class="text-orange-300 hover:text-orange-500"><i class="fas fa-ban text-lg"></i></button>
                            <button onclick="deleteOrder(${i})" class="text-rose-200 hover:text-rose-500"><i class="fas fa-trash text-lg"></i></button>
                        </td>
                    </tr>`;
            });
            document.getElementById('statIncome').innerText = income.toLocaleString();
            document.getElementById('statDue').innerText = dueTotal.toLocaleString();
            document.getElementById('statConfirmed').innerText = successCount;
            document.getElementById('statPending').innerText = pendingCount;
        }

        function settleDue(i) {
            if(orders[i].workStatus === 'Cancelled') return;
            if(orders[i].due > 0) {
                orders[i].paid += orders[i].due;
                orders[i].due = 0;
                renderTable();
            }
        }

        function toggleWork(i) {
            if(orders[i].workStatus === 'Cancelled') return;
            const flow = ['Pending', 'Confirmed', 'Success'];
            let currentIdx = flow.indexOf(orders[i].workStatus);
            orders[i].workStatus = flow[(currentIdx + 1) % flow.length];
            renderTable();
        }

        function cancelOrder(i) {
            if(confirm("Cancel this order? Payments will be removed.")) {
                orders[i].workStatus = 'Cancelled';
                renderTable();
            }
        }

        function deleteOrder(i) { if(confirm("Delete record?")) { orders.splice(i,1); renderTable(); } }

        function searchOrders() {
            const q = document.getElementById('searchInput').value.toLowerCase();
            const filtered = orders.filter(o => o.name.toLowerCase().includes(q) || o.inv.toLowerCase().includes(q));
            renderTable(filtered);
        }

        function viewInvoice(i) {
            const o = orders[i];
            document.getElementById('invN').innerText = o.name; document.getElementById('invI').innerText = o.inv;
            document.getElementById('invP').innerText = o.phone; document.getElementById('invS').innerText = o.service;
            document.getElementById('invT').innerText = `$${o.total.toLocaleString()}`; 
            document.getElementById('invDue').innerText = `$${o.due.toLocaleString()}`;
            document.getElementById('invG').innerText = `$${o.total.toLocaleString()}`; document.getElementById('invD').innerText = o.date;
            document.getElementById('invoiceModal').classList.remove('hidden');
        }
    </script>
</body>
</html>
