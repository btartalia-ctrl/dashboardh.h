<!doctype html>
<html lang="pt-BR">
 <head>
  <meta charset="UTF-8">
  <title>Dashboard Hora a Hora</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://sdk.canva.com/_sdk/element_sdk.js"></script>
  <script src="https://sdk.canva.com/_sdk/data_sdk.js"></script>
  <style>
    body {
      box-sizing: border-box;
    }
    html, body {
      height: 100%;
      width: 100%;
      margin: 0;
      padding: 0;
    }
    .chart-tooltip {
      pointer-events: none;
    }
    .table-scroll::-webkit-scrollbar {
      width: 8px;
    }
    .table-scroll::-webkit-scrollbar-track {
      background: transparent;
    }
    .table-scroll::-webkit-scrollbar-thumb {
      background-color: rgba(0, 0, 0, 0.2);
      border-radius: 9999px;
    }
    .focus-outline:focus-visible {
      outline: 2px solid #0f766e;
      outline-offset: 2px;
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
  <script src="/_sdk/element_sdk.js" type="text/javascript"></script>
 </head>
 <body class="w-full h-full">
  <div id="app-root" class="w-full h-full bg-[#f5f7fb] flex flex-col">
   <header class="w-full px-6 pt-4 pb-3">
    <div class="max-w-6xl mx-auto flex flex-col sm:flex-row sm:items-start sm:justify-between gap-3">
     <div class="flex flex-col gap-1">
      <h1 id="main-title" class="text-2xl font-semibold text-slate-800 tracking-tight">HORA x HORA</h1>
      <p id="subtitle-text" class="text-sm text-slate-500">Análise de capacidade, faturado e perdas por hora</p>
     </div>
     <div class="bg-white rounded-lg shadow-sm px-3 py-2 border border-slate-200 flex items-center gap-3">
      <div>
       <p class="text-[10px] text-slate-500 uppercase tracking-wide mb-0.5">Última atualização</p>
       <p id="last-update-time" class="text-xs font-medium text-slate-700">--</p>
      </div><button id="reset-data-btn" type="button" class="focus-outline px-2 py-1 rounded-md bg-rose-50 border border-rose-200 text-[11px] font-medium text-rose-700 hover:bg-rose-100 transition-colors"> 🔄 Resetar </button>
     </div>
    </div>
   </header>
   <main class="w-full flex-1 overflow-auto">
    <div class="max-w-6xl mx-auto px-6 pb-6 flex flex-col gap-4">
     <section aria-label="Filtros" class="w-full">
      <form id="filters-form" class="bg-white rounded-xl shadow-sm px-4 py-3 flex flex-wrap gap-4 items-end">
       <div class="flex flex-col"><label for="start-hour" class="text-xs font-medium text-slate-600 mb-1">Hora inicial</label> <select id="start-hour" name="start-hour" class="focus-outline border border-slate-200 rounded-lg px-2 py-1.5 text-sm text-slate-700 bg-white"> </select>
       </div>
       <div class="flex flex-col"><label for="end-hour" class="text-xs font-medium text-slate-600 mb-1">Hora final</label> <select id="end-hour" name="end-hour" class="focus-outline border border-slate-200 rounded-lg px-2 py-1.5 text-sm text-slate-700 bg-white"> </select>
       </div>
       <div class="flex flex-col"><label for="loss-type-filter" class="text-xs font-medium text-slate-600 mb-1">Tipo de perda</label> <select id="loss-type-filter" name="loss-type-filter" class="focus-outline border border-slate-200 rounded-lg px-2 py-1.5 text-sm text-slate-700 bg-white"> <option value="ALL">Todos</option> <option value="Transporte">Transporte</option> <option value="CD">CD</option> <option value="Sem perda">Sem perda</option> </select>
       </div>
       <div class="flex-1"></div><button id="distribute-btn" type="button" class="focus-outline inline-flex items-center gap-1 text-sm px-3 py-1.5 rounded-lg bg-emerald-600 text-white hover:bg-emerald-700 shadow-sm font-medium"> <span aria-hidden="true">📊</span> <span>Distribuir Faturado</span> </button> <button id="edit-data-btn" type="button" class="focus-outline inline-flex items-center gap-1 text-sm px-3 py-1.5 rounded-lg bg-white border border-slate-200 text-slate-700 hover:bg-slate-50 shadow-sm"> <span aria-hidden="true">📝</span> <span>Editar Dados</span> </button>
      </form>
     </section>
     <section aria-label="Indicadores de produção" class="w-full">
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
       <article class="bg-white rounded-xl shadow-sm px-4 py-3 flex flex-col gap-1">
        <h2 class="text-xs font-medium text-slate-500 uppercase tracking-wide">Capacidade do dia</h2>
        <p id="kpi-total-capacity" class="text-xl font-semibold text-slate-800">0</p>
        <p class="text-[11px] text-slate-400">Soma da capacidade no intervalo selecionado</p>
       </article>
       <article class="bg-white rounded-xl shadow-sm px-4 py-3 flex flex-col gap-1">
        <h2 class="text-xs font-medium text-slate-500 uppercase tracking-wide">Total faturado</h2>
        <p id="kpi-total-billed" class="text-xl font-semibold text-emerald-600">0</p>
        <p class="text-[11px] text-slate-400">Soma do faturado no intervalo selecionado</p>
       </article>
       <article class="bg-white rounded-xl shadow-sm px-4 py-3 flex flex-col gap-1">
        <h2 class="text-xs font-medium text-slate-500 uppercase tracking-wide">Total de perdas</h2>
        <p id="kpi-total-loss" class="text-xl font-semibold text-rose-600">0</p>
        <p class="text-[11px] text-slate-400">Somatório das perdas no intervalo</p>
       </article>
       <article class="bg-white rounded-xl shadow-sm px-4 py-3 flex flex-col gap-1">
        <h2 class="text-xs font-medium text-slate-500 uppercase tracking-wide">% horas sem perda</h2>
        <p id="kpi-no-loss-percent" class="text-xl font-semibold text-emerald-600">0%</p>
        <p class="text-[11px] text-slate-400">Horas em que não houve perda</p>
       </article>
      </div>
     </section>
     <section aria-label="Análise de Perda - Hora Atual" class="w-full">
      <article class="bg-white rounded-xl shadow-sm px-4 py-3 flex flex-col">
       <div class="flex items-center justify-between mb-3">
        <h2 class="text-sm font-semibold text-slate-700">🕐 Análise de Perda - Hora Atual</h2><span id="current-time-display" class="text-[11px] text-slate-400">--:--</span>
       </div>
       <div id="current-hour-card-container" class="flex justify-center">
       </div>
      </article>
     </section>
     <section aria-label="Gráficos" class="w-full grid grid-cols-1 lg:grid-cols-2 gap-4">
      <article class="bg-white rounded-xl shadow-sm px-4 py-3 flex flex-col">
       <h2 class="text-sm font-semibold text-slate-700 mb-2">Capacidade x Faturado (por hora)</h2>
       <div class="relative w-full" style="height: 320px;">
        <canvas id="line-chart" class="w-full h-full"></canvas>
        <div id="line-tooltip" class="chart-tooltip hidden absolute z-10 bg-white border border-slate-200 rounded-md px-3 py-2 shadow-md text-xs text-slate-700"></div>
       </div>
      </article>
      <article class="bg-white rounded-xl shadow-sm px-4 py-3 flex flex-col">
       <h2 class="text-sm font-semibold text-slate-700 mb-2">Perdas por tipo (por hora)</h2>
       <div class="relative w-full" style="height: 320px;">
        <canvas id="bar-chart" class="w-full h-full"></canvas>
        <div id="bar-tooltip" class="chart-tooltip hidden absolute z-10 bg-white border border-slate-200 rounded-md px-3 py-2 shadow-md text-xs text-slate-700"></div>
       </div>
       <div class="mt-2 flex flex-wrap gap-3 text-[11px] text-slate-500"><span class="inline-flex items-center gap-1"> <span class="w-3 h-3 rounded-sm inline-block" style="background-color:#9ca3af;"></span> Transporte </span> <span class="inline-flex items-center gap-1"> <span class="w-3 h-3 rounded-sm inline-block" style="background-color:#7c3aed;"></span> CD </span> <span class="inline-flex items-center gap-1"> <span class="w-3 h-3 rounded-sm inline-block" style="background-color:#22c55e;"></span> Sem perda </span>
       </div>
      </article>
     </section>
     <section aria-label="Tabela de dados" class="w-full">
      <article class="bg-white rounded-xl shadow-sm px-4 pt-3 pb-2 flex flex-col" style="height: 280px;">
       <div class="flex items-center justify-between mb-2">
        <h2 class="text-sm font-semibold text-slate-700">Dados hora a hora</h2><span class="text-[11px] text-slate-400" id="table-count-label">0 linhas</span>
       </div>
       <div class="flex-1 overflow-auto table-scroll">
        <table class="min-w-full text-xs text-left">
         <thead class="sticky top-0 bg-slate-50 z-10">
          <tr class="text-[11px] text-slate-500 uppercase tracking-wide">
           <th scope="col" class="px-2 py-2 font-medium">Janela</th>
           <th scope="col" class="px-2 py-2 font-medium">Hora</th>
           <th scope="col" class="px-2 py-2 font-medium text-right">Capacidade</th>
           <th scope="col" class="px-2 py-2 font-medium text-right">Faturado</th>
           <th scope="col" class="px-2 py-2 font-medium text-right">Perda</th>
           <th scope="col" class="px-2 py-2 font-medium">Tipo de perda</th>
          </tr>
         </thead>
         <tbody id="data-table-body" class="divide-y divide-slate-100">
         </tbody>
        </table>
       </div>
      </article>
     </section>
    </div>
   </main><!-- Modal de Edição -->
   <div id="edit-modal-backdrop" class="fixed inset-0 bg-black/40 flex items-center justify-center z-20 hidden" style="backdrop-filter: none;">
    <div role="dialog" aria-modal="true" aria-labelledby="edit-modal-title" class="bg-white rounded-xl shadow-lg w-full max-w-3xl max-h-[90%] flex flex-col">
     <header class="px-5 py-3 border-b border-slate-100 flex items-center justify-between">
      <div>
       <h2 id="edit-modal-title" class="text-sm font-semibold text-slate-800">Editar dados por hora</h2>
       <p class="text-[11px] text-slate-500">Altere capacidade, faturado, perda e tipo de perda. As mudanças afetam todo o dashboard.</p>
      </div><button id="close-modal-btn" type="button" class="focus-outline text-slate-400 hover:text-slate-600 text-lg leading-none"> × </button>
     </header>
     <div class="flex-1 overflow-auto px-5 py-3 table-scroll">
      <table class="min-w-full text-xs text-left">
       <thead class="sticky top-0 bg-slate-50 z-10">
        <tr class="text-[11px] text-slate-500 uppercase tracking-wide">
         <th scope="col" class="px-2 py-2 font-medium">Hora</th>
         <th scope="col" class="px-2 py-2 font-medium text-right">Capacidade</th>
         <th scope="col" class="px-2 py-2 font-medium text-right">Faturado</th>
         <th scope="col" class="px-2 py-2 font-medium text-right">Perda</th>
         <th scope="col" class="px-2 py-2 font-medium">Tipo de perda</th>
        </tr>
       </thead>
       <tbody id="edit-table-body" class="divide-y divide-slate-100">
       </tbody>
      </table>
     </div>
     <footer class="px-5 py-3 border-t border-slate-100 flex items-center justify-between"><span id="modal-status" class="text-[11px] text-slate-400"></span>
      <div class="flex gap-2"><button id="cancel-modal-btn" type="button" class="focus-outline px-3 py-1.5 rounded-lg border border-slate-200 text-xs font-medium text-slate-700 bg-white hover:bg-slate-50"> Cancelar </button> <button id="save-modal-btn" type="button" class="focus-outline px-4 py-1.5 rounded-lg bg-emerald-600 text-xs font-semibold text-white hover:bg-emerald-700 disabled:opacity-60 disabled:cursor-not-allowed"> Salvar alterações </button>
      </div>
     </footer>
    </div>
   </div><!-- Modal de Distribuição -->
   <div id="distribution-modal-backdrop" class="fixed inset-0 bg-black/40 flex items-center justify-center z-30 hidden">
    <div role="dialog" aria-modal="true" aria-labelledby="distribution-modal-title" class="bg-white rounded-xl shadow-lg w-full max-w-4xl max-h-[90%] flex flex-col">
     <header class="px-5 py-3 border-b border-slate-100 flex items-center justify-between">
      <div>
       <h2 id="distribution-modal-title" class="text-sm font-semibold text-slate-800">📊 Distribuição de Faturado</h2>
       <p class="text-[11px] text-slate-500">Alocação automática do faturado respeitando a capacidade de cada horário</p>
      </div><button id="close-distribution-btn" type="button" class="focus-outline text-slate-400 hover:text-slate-600 text-lg leading-none"> × </button>
     </header>
     <div class="flex-1 overflow-auto px-5 py-4 table-scroll">
      <div id="distribution-content"></div>
     </div>
     <footer class="px-5 py-3 border-t border-slate-100 flex items-center justify-end gap-2"><button id="cancel-distribution-btn" type="button" class="focus-outline px-4 py-1.5 rounded-lg border border-slate-200 text-xs font-medium text-slate-700 bg-white hover:bg-slate-50"> Cancelar </button> <button id="apply-distribution-btn" type="button" class="focus-outline px-4 py-1.5 rounded-lg bg-emerald-600 text-xs font-semibold text-white hover:bg-emerald-700"> Aplicar Distribuição </button>
     </footer>
    </div>
   </div>
  </div>
  <script>
    const COLORS = {
      BACKGROUND: '#f5f7fb',
      SURFACE: '#ffffff',
      TEXT: '#0f172a',
      PRIMARY_ACTION: '#10b981',
      SECONDARY_ACTION: '#64748b'
    };

    const defaultConfig = {
      main_title: "HORA x HORA",
      subtitle: "Análise de capacidade, faturado e perdas por hora",
      background_color: COLORS.BACKGROUND,
      surface_color: COLORS.SURFACE,
      text_color: COLORS.TEXT,
      primary_action_color: COLORS.PRIMARY_ACTION,
      secondary_action_color: COLORS.SECONDARY_ACTION,
      font_family: "Inter",
      font_size: 14
    };

    const baseRawData = [
      { window: "00:00-01:00", hourLabel: "00:00", capacity: 0 },
      { window: "01:00-02:00", hourLabel: "01:00", capacity: 0 },
      { window: "02:00-03:00", hourLabel: "02:00", capacity: 3000 },
      { window: "03:00-04:00", hourLabel: "03:00", capacity: 4500 },
      { window: "04:00-05:00", hourLabel: "04:00", capacity: 4500 },
      { window: "05:00-06:00", hourLabel: "05:00", capacity: 4500 },
      { window: "06:00-07:00", hourLabel: "06:00", capacity: 0 },
      { window: "07:00-08:00", hourLabel: "07:00", capacity: 3000 },
      { window: "08:00-09:00", hourLabel: "08:00", capacity: 3500 },
      { window: "09:00-10:00", hourLabel: "09:00", capacity: 3500 },
      { window: "10:00-11:00", hourLabel: "10:00", capacity: 3500 },
      { window: "11:00-12:00", hourLabel: "11:00", capacity: 3000 },
      { window: "12:00-13:00", hourLabel: "12:00", capacity: 3000 },
      { window: "13:00-14:00", hourLabel: "13:00", capacity: 5000 },
      { window: "14:00-15:00", hourLabel: "14:00", capacity: 5000 },
      { window: "15:00-16:00", hourLabel: "15:00", capacity: 6000 },
      { window: "16:00-17:00", hourLabel: "16:00", capacity: 6500 },
      { window: "17:00-18:00", hourLabel: "17:00", capacity: 6500 },
      { window: "18:00-19:00", hourLabel: "18:00", capacity: 5000 },
      { window: "19:00-20:00", hourLabel: "19:00", capacity: 5000 },
      { window: "20:00-21:00", hourLabel: "20:00", capacity: 5000 },
      { window: "21:00-22:00", hourLabel: "21:00", capacity: 5000 },
      { window: "22:00-23:00", hourLabel: "22:00", capacity: 2000 },
      { window: "23:00-00:00", hourLabel: "23:00", capacity: 0 }
    ];

    let workingData = baseRawData.map((row, idx) => ({
      id: idx,
      window: row.window,
      hourLabel: row.hourLabel,
      capacity: row.capacity,
      billed: 0,
      loss: 0,
      lossType: "Sem perda"
    }));

    const filterState = {
      startHourIndex: 0,
      endHourIndex: 23,
      lossType: "ALL"
    };

    let dataSdkInitialized = false;
    let distributionResult = null;

    function formatNumber(num) {
      return num.toLocaleString('pt-BR');
    }

    function getCurrentHourIndex() {
      const now = new Date();
      const currentHour = now.getHours();
      return currentHour;
    }

    function updateCurrentTimeDisplay() {
      const now = new Date();
      const timeStr = now.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit', second: '2-digit' });
      document.getElementById('current-time-display').textContent = timeStr;
    }

    function renderCurrentHourCard() {
      const container = document.getElementById('current-hour-card-container');
      container.innerHTML = "";
      
      const currentHourIndex = getCurrentHourIndex();
      
      // Calcula a distribuição automática
      const distribution = calculateDistribution();
      const currentDistRow = distribution.distribution[currentHourIndex];
      
      if (!currentDistRow) {
        container.innerHTML = '<p class="text-xs text-slate-400 text-center py-4">Horário não encontrado</p>';
        return;
      }
      
      const capacity = currentDistRow.capacity;
      const billedAllocated = currentDistRow.allocated;
      const receivedBilled = currentDistRow.receivedBilled;
      const ownBilled = currentDistRow.ownBilled;
      const delta = currentDistRow.delta;
      
      const hasLoss = billedAllocated < capacity;
      const lossAmount = hasLoss ? capacity - billedAllocated : 0;
      const receivedCarryOver = receivedBilled > ownBilled;
      
      const card = document.createElement('div');
      
      let bgColor, borderColor, statusIcon, statusText, statusColor;
      
      if (hasLoss) {
        bgColor = 'bg-rose-50';
        borderColor = 'border-rose-300';
        statusIcon = '❌';
        statusText = 'Sim';
        statusColor = 'text-rose-700';
      } else {
        bgColor = 'bg-emerald-50';
        borderColor = 'border-emerald-300';
        statusIcon = '✅';
        statusText = 'Não';
        statusColor = 'text-emerald-700';
      }
      
      card.className = `${bgColor} border-2 ${borderColor} rounded-xl px-6 py-5 flex flex-col gap-3 max-w-md w-full shadow-lg`;
      card.innerHTML = `
        <div class="flex items-center justify-between mb-2">
          <span class="text-3xl font-bold text-slate-800">${currentDistRow.hour}</span>
          <span class="text-4xl" aria-hidden="true">${statusIcon}</span>
        </div>
        
        <div class="flex flex-col gap-2 text-sm text-slate-700">
          <div class="flex justify-between items-center py-2 border-b border-slate-200">
            <span class="text-slate-600 font-medium">Capacidade:</span>
            <span class="font-bold text-lg">${formatNumber(capacity)}</span>
          </div>
          ${receivedCarryOver ? `
          <div class="flex justify-between items-center py-2 border-b border-slate-200">
            <span class="text-slate-600 font-medium">Faturado Recebido:</span>
            <span class="font-bold text-lg text-amber-700">${formatNumber(receivedBilled)} <span class="text-xs">↓</span></span>
          </div>
          ` : ''}
          <div class="flex justify-between items-center py-2 border-b border-slate-200">
            <span class="text-slate-600 font-medium">Faturado Alocado:</span>
            <span class="font-bold text-lg ${billedAllocated > 0 ? 'text-emerald-700' : ''}">${formatNumber(billedAllocated)}</span>
          </div>
          ${hasLoss ? `
          <div class="flex justify-between items-center py-2 border-b border-slate-200">
            <span class="text-slate-600 font-medium">Perda:</span>
            <span class="font-bold text-lg text-rose-600">${formatNumber(lossAmount)}</span>
          </div>
          ` : ''}
          ${delta > 0 ? `
          <div class="flex justify-between items-center py-2 border-b border-slate-200">
            <span class="text-slate-600 font-medium">Excedente (p/ frente):</span>
            <span class="font-bold text-lg text-rose-600">${formatNumber(delta)} <span class="text-xs">→</span></span>
          </div>
          ` : ''}
        </div>
        
        <div class="mt-2 pt-3 border-t-2 border-slate-300 flex justify-between items-center">
          <span class="text-sm text-slate-600 font-semibold">Status de Perda:</span>
          <span class="text-xl font-bold ${statusColor}">${statusText}</span>
        </div>
      `;
      
      container.appendChild(card);
    }

    function initHourSelects() {
      const startSel = document.getElementById('start-hour');
      const endSel = document.getElementById('end-hour');
      startSel.innerHTML = "";
      endSel.innerHTML = "";
      workingData.forEach((row, idx) => {
        const opt1 = document.createElement('option');
        opt1.value = String(idx);
        opt1.textContent = row.hourLabel;
        if (idx === filterState.startHourIndex) opt1.selected = true;
        startSel.appendChild(opt1);

        const opt2 = document.createElement('option');
        opt2.value = String(idx);
        opt2.textContent = row.hourLabel;
        if (idx === filterState.endHourIndex) opt2.selected = true;
        endSel.appendChild(opt2);
      });
    }

    function getFilteredData() {
      return workingData.filter((row, idx) => {
        const withinRange = idx >= filterState.startHourIndex && idx <= filterState.endHourIndex;
        const typeMatch = filterState.lossType === "ALL" || row.lossType === filterState.lossType;
        return withinRange && typeMatch;
      });
    }

    function updateKpis() {
      const filtered = getFilteredData();
      const totalCapacity = filtered.reduce((sum, r) => sum + (Number(r.capacity) || 0), 0);
      const totalBilled = filtered.reduce((sum, r) => sum + (Number(r.billed) || 0), 0);
      const totalLoss = filtered.reduce((sum, r) => sum + (Number(r.loss) || 0), 0);
      const hoursCount = filtered.length;
      const noLossHours = filtered.filter(r => (Number(r.loss) || 0) === 0).length;
      const percentNoLoss = hoursCount === 0 ? 0 : Math.round((noLossHours / hoursCount) * 100);

      document.getElementById('kpi-total-capacity').textContent = formatNumber(totalCapacity);
      document.getElementById('kpi-total-billed').textContent = formatNumber(totalBilled);
      document.getElementById('kpi-total-loss').textContent = formatNumber(totalLoss);
      document.getElementById('kpi-no-loss-percent').textContent = percentNoLoss + "%";
    }

    function renderTable() {
      const tbody = document.getElementById('data-table-body');
      tbody.innerHTML = "";
      const filtered = getFilteredData();
      filtered.forEach(row => {
        const tr = document.createElement('tr');
        let bgClass = "";
        if (row.lossType === "Transporte") {
          bgClass = "bg-slate-100";
        } else if (row.lossType === "CD") {
          bgClass = "bg-violet-50";
        } else if (row.lossType === "Sem perda") {
          bgClass = "bg-emerald-50/40";
        }
        tr.className = bgClass + " hover:bg-slate-100/70 transition-colors";

        tr.innerHTML = `
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px] text-slate-700">${row.window}</td>
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px] text-slate-700">${row.hourLabel}</td>
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px] text-right text-slate-700">${formatNumber(Number(row.capacity || 0))}</td>
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px] text-right text-slate-700">${formatNumber(Number(row.billed || 0))}</td>
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px] text-right text-slate-700">${formatNumber(Number(row.loss || 0))}</td>
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px] text-slate-700">${row.lossType}</td>
        `;
        tbody.appendChild(tr);
      });
      document.getElementById('table-count-label').textContent = filtered.length + " linhas";
    }

    function createLineChart() {
      const canvas = document.getElementById('line-chart');
      const ctx = canvas.getContext('2d');
      const dpr = window.devicePixelRatio || 1;
      const width = canvas.clientWidth;
      const height = canvas.clientHeight;
      canvas.width = width * dpr;
      canvas.height = height * dpr;
      ctx.scale(dpr, dpr);

      const filtered = getFilteredData();
      const labels = filtered.map(r => r.hourLabel);
      const capacityValues = filtered.map(r => Number(r.capacity || 0));
      const billedValues = filtered.map(r => Number(r.billed || 0));
      const maxVal = Math.max(1, ...capacityValues, ...billedValues);
      const paddingLeft = 36;
      const paddingRight = 18;
      const paddingTop = 20;
      const paddingBottom = 28;
      const plotWidth = width - paddingLeft - paddingRight;
      const plotHeight = height - paddingTop - paddingBottom;

      ctx.clearRect(0, 0, width, height);
      ctx.fillStyle = "#ffffff";
      ctx.fillRect(0, 0, width, height);

      ctx.strokeStyle = "#e5e7eb";
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.moveTo(paddingLeft, paddingTop);
      ctx.lineTo(paddingLeft, height - paddingBottom);
      ctx.lineTo(width - paddingRight, height - paddingBottom);
      ctx.stroke();

      ctx.fillStyle = "#6b7280";
      ctx.font = "10px system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif";
      const steps = 4;
      for (let i = 0; i <= steps; i++) {
        const value = (maxVal / steps) * i;
        const y = (height - paddingBottom) - (plotHeight * (value / maxVal));
        ctx.strokeStyle = "#f3f4f6";
        ctx.beginPath();
        ctx.moveTo(paddingLeft, y);
        ctx.lineTo(width - paddingRight, y);
        ctx.stroke();

        ctx.fillStyle = "#9ca3af";
        ctx.textAlign = "right";
        ctx.textBaseline = "middle";
        ctx.fillText(formatNumber(Math.round(value)), paddingLeft - 4, y);
      }

      ctx.fillStyle = "#9ca3af";
      ctx.textAlign = "center";
      ctx.textBaseline = "top";
      const count = labels.length;
      if (count > 0) {
        for (let i = 0; i < count; i++) {
          const x = paddingLeft + (plotWidth * (count === 1 ? 0.5 : (i / (count - 1))));
          if (count <= 8 || i % Math.ceil(count / 8) === 0) {
            ctx.fillText(labels[i], x, height - paddingBottom + 6);
          }
        }
      }

      function pointForIndex(idx, value) {
        const x = paddingLeft + (plotWidth * (count === 1 ? 0.5 : (idx / (count - 1))));
        const y = (height - paddingBottom) - (plotHeight * (value / maxVal));
        return { x, y };
      }

      ctx.lineWidth = 2;

      ctx.strokeStyle = "#3b82f6";
      ctx.beginPath();
      capacityValues.forEach((val, idx) => {
        const { x, y } = pointForIndex(idx, val);
        if (idx === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);
      });
      ctx.stroke();

      ctx.strokeStyle = "#22c55e";
      ctx.beginPath();
      billedValues.forEach((val, idx) => {
        const { x, y } = pointForIndex(idx, val);
        if (idx === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);
      });
      ctx.stroke();

      for (let i = 0; i < count; i++) {
        const capPoint = pointForIndex(i, capacityValues[i]);
        const billPoint = pointForIndex(i, billedValues[i]);

        ctx.fillStyle = "#3b82f6";
        ctx.beginPath();
        ctx.arc(capPoint.x, capPoint.y, 3, 0, Math.PI * 2);
        ctx.fill();

        ctx.fillStyle = "#22c55e";
        ctx.beginPath();
        ctx.arc(billPoint.x, billPoint.y, 3, 0, Math.PI * 2);
        ctx.fill();
      }

      const hitBoxes = filtered.map((row, idx) => {
        const x = paddingLeft + (plotWidth * (count === 1 ? 0.5 : (idx / (count - 1))));
        return { x, row, idx };
      });

      const tooltipEl = document.getElementById('line-tooltip');
      canvas.onmousemove = (e) => {
        const rect = canvas.getBoundingClientRect();
        const x = (e.clientX - rect.left);
        const y = (e.clientY - rect.top);
        if (y < paddingTop || y > height - paddingBottom) {
          tooltipEl.classList.add('hidden');
          return;
        }
        let nearest = null;
        let minDist = Infinity;
        hitBoxes.forEach(h => {
          const dx = h.x - x;
          const d = Math.abs(dx);
          if (d < minDist) {
            minDist = d;
            nearest = h;
          }
        });
        if (!nearest || minDist > 25) {
          tooltipEl.classList.add('hidden');
          return;
        }
        const r = nearest.row;
        const lossVal = (Number(r.capacity || 0) - Number(r.billed || 0)) || 0;
        tooltipEl.innerHTML = `
          <div class="font-semibold text-[11px] mb-1">${r.hourLabel}</div>
          <div>Capacidade: <span class="font-medium text-slate-900">${formatNumber(Number(r.capacity || 0))}</span></div>
          <div>Faturado: <span class="font-medium text-slate-900">${formatNumber(Number(r.billed || 0))}</span></div>
          <div>Perda: <span class="font-medium text-rose-600">${formatNumber(lossVal)}</span></div>
        `;
        const ttRect = tooltipEl.getBoundingClientRect();
        const ttWidth = ttRect.width || 140;
        const ttHeight = ttRect.height || 60;
        let left = rect.left + nearest.x - ttWidth / 2;
        let top = rect.top + paddingTop + 10;
        tooltipEl.style.left = left + "px";
        tooltipEl.style.top = top + "px";
        tooltipEl.classList.remove('hidden');
      };
      canvas.onmouseleave = () => {
        tooltipEl.classList.add('hidden');
      };
    }

    function createBarChart() {
      const canvas = document.getElementById('bar-chart');
      const ctx = canvas.getContext('2d');
      const dpr = window.devicePixelRatio || 1;
      const width = canvas.clientWidth;
      const height = canvas.clientHeight;
      canvas.width = width * dpr;
      canvas.height = height * dpr;
      ctx.scale(dpr, dpr);

      const filtered = getFilteredData();
      const labels = filtered.map(r => r.hourLabel);

      const stacks = filtered.map(r => {
        const totalCap = Number(r.capacity || 0);
        const loss = Number(r.loss || 0);
        const billed = Number(r.billed || 0);
        let transporte = 0;
        let cd = 0;
        let semPerda = 0;

        if (r.lossType === "Sem perda") {
          semPerda = totalCap;
        } else if (r.lossType === "Transporte") {
          transporte = Math.min(loss, totalCap);
          semPerda = Math.max(totalCap - transporte, 0);
        } else if (r.lossType === "CD") {
          cd = Math.min(loss, totalCap);
          semPerda = Math.max(totalCap - cd, 0);
        } else {
          semPerda = totalCap;
        }
        return { transporte, cd, semPerda, raw: r };
      });

      const maxStack = Math.max(1, ...stacks.map(s => s.transporte + s.cd + s.semPerda));
      const paddingLeft = 28;
      const paddingRight = 10;
      const paddingTop = 18;
      const paddingBottom = 32;
      const plotWidth = width - paddingLeft - paddingRight;
      const plotHeight = height - paddingTop - paddingBottom;

      ctx.clearRect(0, 0, width, height);
      ctx.fillStyle = "#ffffff";
      ctx.fillRect(0, 0, width, height);

      ctx.strokeStyle = "#e5e7eb";
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.moveTo(paddingLeft, paddingTop);
      ctx.lineTo(paddingLeft, height - paddingBottom);
      ctx.lineTo(width - paddingRight, height - paddingBottom);
      ctx.stroke();

      ctx.fillStyle = "#6b7280";
      ctx.font = "10px system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif";
      const steps = 4;
      for (let i = 0; i <= steps; i++) {
        const value = (maxStack / steps) * i;
        const y = (height - paddingBottom) - (plotHeight * (value / maxStack));
        ctx.strokeStyle = "#f3f4f6";
        ctx.beginPath();
        ctx.moveTo(paddingLeft, y);
        ctx.lineTo(width - paddingRight, y);
        ctx.stroke();

        ctx.fillStyle = "#9ca3af";
        ctx.textAlign = "right";
        ctx.textBaseline = "middle";
        ctx.fillText(formatNumber(Math.round(value)), paddingLeft - 4, y);
      }

      const count = labels.length;
      const barAreaWidth = plotWidth / Math.max(count, 1);
      const barWidth = Math.min(18, barAreaWidth * 0.55);
      const barHitBoxes = [];

      ctx.textAlign = "center";
      ctx.textBaseline = "top";
      ctx.fillStyle = "#9ca3af";

      for (let i = 0; i < count; i++) {
        const centerX = paddingLeft + (barAreaWidth * (i + 0.5));
        const x0 = centerX - barWidth / 2;
        const stack = stacks[i];
        const totalHeight = plotHeight * ((stack.transporte + stack.cd + stack.semPerda) / maxStack);
        let currentY = height - paddingBottom;

        if (stack.transporte > 0) {
          const h = plotHeight * (stack.transporte / maxStack);
          ctx.fillStyle = "#9ca3af";
          ctx.fillRect(x0, currentY - h, barWidth, h);
          currentY -= h;
        }
        if (stack.cd > 0) {
          const h = plotHeight * (stack.cd / maxStack);
          ctx.fillStyle = "#7c3aed";
          ctx.fillRect(x0, currentY - h, barWidth, h);
          currentY -= h;
        }
        if (stack.semPerda > 0) {
          const h = plotHeight * (stack.semPerda / maxStack);
          ctx.fillStyle = "#22c55e";
          ctx.fillRect(x0, currentY - h, barWidth, h);
          currentY -= h;
        }

        if (count <= 12 || i % Math.ceil(count / 12) === 0) {
          ctx.fillStyle = "#9ca3af";
          ctx.fillText(labels[i], centerX, height - paddingBottom + 6);
        }

        barHitBoxes.push({
          x: x0,
          y: (height - paddingBottom) - totalHeight,
          w: barWidth,
          h: totalHeight,
          stack,
          label: labels[i]
        });
      }

      const tooltipEl = document.getElementById('bar-tooltip');
      canvas.onmousemove = (e) => {
        const rect = canvas.getBoundingClientRect();
        const x = (e.clientX - rect.left);
        const y = (e.clientY - rect.top);

        let target = null;
        for (const box of barHitBoxes) {
          if (x >= box.x && x <= box.x + box.w && y >= box.y && y <= box.y + box.h) {
            target = box;
            break;
          }
        }
        if (!target) {
          tooltipEl.classList.add('hidden');
          return;
        }
        const s = target.stack;
        tooltipEl.innerHTML = `
          <div class="font-semibold text-[11px] mb-1">${target.label}</div>
          <div>Transporte: <span class="font-medium text-slate-900">${formatNumber(s.transporte)}</span></div>
          <div>CD: <span class="font-medium text-slate-900">${formatNumber(s.cd)}</span></div>
          <div>Sem perda: <span class="font-medium text-slate-900">${formatNumber(s.semPerda)}</span></div>
        `;
        const ttRect = tooltipEl.getBoundingClientRect();
        const ttWidth = ttRect.width || 160;
        const ttHeight = ttRect.height || 70;
        let left = rect.left + (target.x + target.w / 2) - ttWidth / 2;
        let top = rect.top + target.y - ttHeight - 8;
        if (top < rect.top + 4) top = rect.top + 4;
        tooltipEl.style.left = left + "px";
        tooltipEl.style.top = top + "px";
        tooltipEl.classList.remove('hidden');
      };
      canvas.onmouseleave = () => {
        tooltipEl.classList.add('hidden');
      };
    }

    function renderEditTable() {
      const tbody = document.getElementById('edit-table-body');
      tbody.innerHTML = "";
      workingData.forEach((row, idx) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px] text-slate-700">${row.hourLabel}</td>
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px] text-right">
            <label class="sr-only" for="cap-${idx}">Capacidade ${row.hourLabel}</label>
            <input id="cap-${idx}" data-idx="${idx}" type="number" min="0" class="focus-outline w-24 text-right border border-slate-200 rounded-md px-1 py-0.5 text-[11px] text-slate-700 bg-white" value="${row.capacity}">
          </td>
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px] text-right">
            <label class="sr-only" for="bill-${idx}">Faturado ${row.hourLabel}</label>
            <input id="bill-${idx}" data-idx="${idx}" type="number" min="0" class="focus-outline w-24 text-right border border-slate-200 rounded-md px-1 py-0.5 text-[11px] text-slate-700 bg-white" value="${row.billed}">
          </td>
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px] text-right">
            <label class="sr-only" for="loss-${idx}">Perda ${row.hourLabel}</label>
            <input id="loss-${idx}" data-idx="${idx}" type="number" min="0" class="focus-outline w-24 text-right border border-slate-200 rounded-md px-1 py-0.5 text-[11px] text-slate-700 bg-white" value="${row.loss}">
          </td>
          <td class="px-2 py-1.5 whitespace-nowrap text-[11px]">
            <label class="sr-only" for="type-${idx}">Tipo de perda ${row.hourLabel}</label>
            <select id="type-${idx}" data-idx="${idx}" class="focus-outline w-28 border border-slate-200 rounded-md px-1 py-0.5 text-[11px] text-slate-700 bg-white">
              <option value="Transporte" ${row.lossType === "Transporte" ? "selected" : ""}>Transporte</option>
              <option value="CD" ${row.lossType === "CD" ? "selected" : ""}>CD</option>
              <option value="Sem perda" ${row.lossType === "Sem perda" ? "selected" : ""}>Sem perda</option>
            </select>
          </td>
        `;
        tbody.appendChild(tr);
      });
    }

    function openModal() {
      renderEditTable();
      document.getElementById('edit-modal-backdrop').classList.remove('hidden');
      document.getElementById('modal-status').textContent = "";
    }

    function closeModal() {
      document.getElementById('edit-modal-backdrop').classList.add('hidden');
    }

    function calculateDistribution() {
      const distribution = [];
      let carryOver = 0;
      
      for (let i = 0; i < workingData.length; i++) {
        const row = workingData[i];
        const capacity = Number(row.capacity || 0);
        const ownBilled = Number(row.billed || 0);
        
        const receivedBilled = ownBilled + carryOver;
        
        const allocated = Math.min(receivedBilled, capacity);
        const delta = Math.max(0, receivedBilled - allocated);
        
        distribution.push({
          hour: row.hourLabel,
          window: row.window,
          capacity: capacity,
          ownBilled: ownBilled,
          receivedBilled: receivedBilled,
          allocated: allocated,
          delta: delta
        });
        
        carryOver = delta;
      }
      
      const totalOriginalBilled = workingData.reduce((sum, r) => sum + Number(r.billed || 0), 0);
      const totalAllocated = distribution.reduce((sum, d) => sum + d.allocated, 0);
      const fullyAllocated = carryOver === 0;
      
      return {
        distribution,
        totalOriginalBilled,
        totalAllocated,
        finalCarryOver: carryOver,
        fullyAllocated
      };
    }

    function renderDistributionTable(result) {
      const content = document.getElementById('distribution-content');
      
      let html = `
        <div class="mb-4 p-3 bg-blue-50 border border-blue-200 rounded-lg">
          <p class="text-sm font-semibold text-blue-900 mb-1">📋 Resumo da Distribuição</p>
          <p class="text-xs text-blue-800">Total faturado original: <strong>${formatNumber(result.totalOriginalBilled)}</strong></p>
          <p class="text-xs text-blue-800">Total alocado: <strong>${formatNumber(result.totalAllocated)}</strong></p>
          ${result.finalCarryOver > 0 ? `<p class="text-xs text-rose-700 font-medium mt-1">⚠️ Excedente final não alocado: <strong>${formatNumber(result.finalCarryOver)}</strong></p>` : ''}
          <p class="text-xs ${result.fullyAllocated ? 'text-emerald-700' : 'text-rose-700'} font-medium mt-1">
            ${result.fullyAllocated ? '✅ Todo o faturado foi alocado com sucesso!' : '⚠️ Capacidade insuficiente para alocar todo o faturado'}
          </p>
        </div>
        
        <div class="overflow-x-auto">
          <table class="min-w-full text-xs border-collapse">
            <thead class="bg-slate-100">
              <tr>
                <th class="px-3 py-2 text-left font-semibold text-slate-700 border border-slate-200">Horário</th>
                <th class="px-3 py-2 text-left font-semibold text-slate-700 border border-slate-200">Janela</th>
                <th class="px-3 py-2 text-right font-semibold text-slate-700 border border-slate-200">Capacidade</th>
                <th class="px-3 py-2 text-right font-semibold text-slate-700 border border-slate-200">Faturado Recebido</th>
                <th class="px-3 py-2 text-right font-semibold text-slate-700 border border-slate-200">Faturado Alocado</th>
                <th class="px-3 py-2 text-right font-semibold text-slate-700 border border-slate-200">Delta (p/ frente)</th>
              </tr>
            </thead>
            <tbody>
      `;
      
      result.distribution.forEach((row, idx) => {
        const isFullyUsed = row.allocated === row.capacity && row.capacity > 0;
        const hasCarryOver = row.delta > 0;
        const receivedCarryOver = row.receivedBilled > row.ownBilled;
        
        let rowClass = '';
        if (isFullyUsed) {
          rowClass = 'bg-emerald-50';
        } else if (row.capacity === 0) {
          rowClass = 'bg-slate-50';
        } else if (receivedCarryOver) {
          rowClass = 'bg-amber-50';
        }
        
        html += `
          <tr class="${rowClass}">
            <td class="px-3 py-2 border border-slate-200 font-medium text-slate-800">${row.hour}</td>
            <td class="px-3 py-2 border border-slate-200 text-slate-600">${row.window}</td>
            <td class="px-3 py-2 border border-slate-200 text-right ${row.capacity === 0 ? 'text-slate-400' : 'text-slate-700'}">${formatNumber(row.capacity)}</td>
            <td class="px-3 py-2 border border-slate-200 text-right ${receivedCarryOver ? 'text-amber-700 font-semibold' : 'text-slate-700'}">
              ${formatNumber(row.receivedBilled)}
              ${receivedCarryOver ? '<span class="text-[10px] text-amber-600 ml-1">↓</span>' : ''}
            </td>
            <td class="px-3 py-2 border border-slate-200 text-right font-semibold ${row.allocated > 0 ? 'text-emerald-700' : 'text-slate-400'}">${formatNumber(row.allocated)}</td>
            <td class="px-3 py-2 border border-slate-200 text-right ${hasCarryOver ? 'text-rose-600 font-semibold' : 'text-slate-400'}">
              ${formatNumber(row.delta)}
              ${hasCarryOver ? '<span class="text-[10px] text-rose-600 ml-1">→</span>' : ''}
            </td>
          </tr>
        `;
      });
      
      html += `
            </tbody>
          </table>
        </div>
        
        <div class="mt-4 p-3 bg-slate-50 border border-slate-200 rounded-lg">
          <p class="text-xs text-slate-600 mb-2"><strong>Legenda:</strong></p>
          <div class="flex flex-col gap-1 text-xs text-slate-600">
            <div class="flex items-center gap-2">
              <span class="w-4 h-4 bg-emerald-50 border border-emerald-200 rounded"></span>
              <span>Horário com capacidade totalmente utilizada</span>
            </div>
            <div class="flex items-center gap-2">
              <span class="w-4 h-4 bg-amber-50 border border-amber-200 rounded"></span>
              <span>Horário que recebeu excedente da hora anterior (↓)</span>
            </div>
            <div class="flex items-center gap-2">
              <span class="w-4 h-4 bg-slate-50 border border-slate-200 rounded"></span>
              <span>Horário sem capacidade</span>
            </div>
            <div class="flex items-center gap-2">
              <span class="text-rose-600 font-bold">→</span>
              <span>Indica que há excedente sendo transferido para o próximo horário</span>
            </div>
          </div>
        </div>
      `;
      
      content.innerHTML = html;
    }

    function openDistributionModal() {
      distributionResult = calculateDistribution();
      renderDistributionTable(distributionResult);
      document.getElementById('distribution-modal-backdrop').classList.remove('hidden');
    }

    function closeDistributionModal() {
      document.getElementById('distribution-modal-backdrop').classList.add('hidden');
      distributionResult = null;
    }

    async function applyDistribution() {
      if (!distributionResult) return;
      
      const applyBtn = document.getElementById('apply-distribution-btn');
      applyBtn.disabled = true;
      applyBtn.textContent = 'Aplicando...';
      
      distributionResult.distribution.forEach((distRow, idx) => {
        if (idx < workingData.length) {
          workingData[idx].billed = distRow.allocated;
        }
      });
      
      if (dataSdkInitialized && window.dataSdk) {
        for (const row of workingData) {
          if (row.__backendId) {
            const dataRecord = {
              hour_label: row.hourLabel,
              capacity: row.capacity,
              billed: row.billed,
              loss: row.loss,
              loss_type: row.lossType,
              __backendId: row.__backendId
            };

            const result = await window.dataSdk.update(dataRecord);
            if (result.isError) {
              console.error("Erro ao atualizar:", result.error);
              applyBtn.textContent = 'Erro ao salvar!';
              applyBtn.disabled = false;
              return;
            }
          } else {
            const dataRecord = {
              hour_label: row.hourLabel,
              capacity: row.capacity,
              billed: row.billed,
              loss: row.loss,
              loss_type: row.lossType
            };

            const result = await window.dataSdk.create(dataRecord);
            if (result.isError) {
              console.error("Erro ao criar:", result.error);
              applyBtn.textContent = 'Erro ao salvar!';
              applyBtn.disabled = false;
              return;
            }
          }
        }
      }
      
      updateLastUpdateTime();
      updateKpis();
      renderCurrentHourCard();
      renderTable();
      createLineChart();
      createBarChart();
      
      applyBtn.textContent = '✅ Aplicado!';
      
      setTimeout(() => {
        closeDistributionModal();
        applyBtn.textContent = 'Aplicar Distribuição';
        applyBtn.disabled = false;
      }, 800);
    }

    async function saveModalChanges() {
      const statusEl = document.getElementById('modal-status');
      const saveBtn = document.getElementById('save-modal-btn');
      
      statusEl.textContent = "Salvando...";
      saveBtn.disabled = true;

      // Captura os valores dos inputs ANTES de qualquer operação
      const updatedValues = [];
      for (let idx = 0; idx < workingData.length; idx++) {
        const capInput = document.getElementById('cap-' + idx);
        const billInput = document.getElementById('bill-' + idx);
        const lossInput = document.getElementById('loss-' + idx);
        const typeSelect = document.getElementById('type-' + idx);
        
        updatedValues.push({
          capacity: Number(capInput.value || 0),
          billed: Number(billInput.value || 0),
          loss: Number(lossInput.value || 0),
          lossType: typeSelect.value || "Sem perda"
        });
      }

      // Salva no Data SDK se disponível
      if (dataSdkInitialized && window.dataSdk) {
        // Primeiro, deleta todos os registros existentes
        const recordsToDelete = workingData.filter(row => row.__backendId);
        for (const row of recordsToDelete) {
          const deleteRecord = {
            hour_label: row.hourLabel,
            capacity: row.capacity,
            billed: row.billed,
            loss: row.loss,
            loss_type: row.lossType,
            __backendId: row.__backendId
          };
          
          const deleteResult = await window.dataSdk.delete(deleteRecord);
          if (deleteResult.isError) {
            console.error("Erro ao deletar:", deleteResult.error);
          }
        }
        
        // Atualiza os dados locais com os valores capturados
        for (let idx = 0; idx < workingData.length; idx++) {
          const row = workingData[idx];
          delete row.__backendId;
          row.capacity = updatedValues[idx].capacity;
          row.billed = updatedValues[idx].billed;
          row.loss = updatedValues[idx].loss;
          row.lossType = updatedValues[idx].lossType;
        }
        
        // Agora cria novos registros com os dados atualizados
        for (const row of workingData) {
          const dataRecord = {
            hour_label: row.hourLabel,
            capacity: row.capacity,
            billed: row.billed,
            loss: row.loss,
            loss_type: row.lossType
          };

          const result = await window.dataSdk.create(dataRecord);
          if (result.isError) {
            console.error("Erro ao criar:", result.error);
            statusEl.textContent = "Erro ao salvar!";
            saveBtn.disabled = false;
            return;
          }
        }
      } else {
        // Se não tiver SDK, apenas atualiza localmente
        for (let idx = 0; idx < workingData.length; idx++) {
          const row = workingData[idx];
          row.capacity = updatedValues[idx].capacity;
          row.billed = updatedValues[idx].billed;
          row.loss = updatedValues[idx].loss;
          row.lossType = updatedValues[idx].lossType;
        }
      }

      updateLastUpdateTime();
      updateKpis();
      renderCurrentHourCard();
      renderTable();
      createLineChart();
      createBarChart();

      statusEl.textContent = "✅ Alterações salvas!";
      saveBtn.disabled = false;

      setTimeout(() => {
        closeModal();
      }, 800);
    }

    function updateLastUpdateTime() {
      const now = new Date();
      const dateStr = now.toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric' });
      const timeStr = now.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });
      document.getElementById('last-update-time').textContent = `${dateStr} às ${timeStr}`;
    }

    function initFilterEvents() {
      const startSel = document.getElementById('start-hour');
      const endSel = document.getElementById('end-hour');
      const lossSel = document.getElementById('loss-type-filter');

      startSel.addEventListener('change', (e) => {
        const val = parseInt(e.target.value, 10);
        filterState.startHourIndex = isNaN(val) ? 0 : val;
        if (filterState.startHourIndex > filterState.endHourIndex) {
          filterState.endHourIndex = filterState.startHourIndex;
          endSel.value = String(filterState.endHourIndex);
        }
        updateKpis();
        renderTable();
        createLineChart();
        createBarChart();
      });

      endSel.addEventListener('change', (e) => {
        const val = parseInt(e.target.value, 10);
        filterState.endHourIndex = isNaN(val) ? workingData.length - 1 : val;
        if (filterState.endHourIndex < filterState.startHourIndex) {
          filterState.startHourIndex = filterState.endHourIndex;
          startSel.value = String(filterState.startHourIndex);
        }
        updateKpis();
        renderTable();
        createLineChart();
        createBarChart();
      });

      lossSel.addEventListener('change', (e) => {
        filterState.lossType = e.target.value;
        updateKpis();
        renderTable();
        createLineChart();
        createBarChart();
      });

      document.getElementById('distribute-btn').addEventListener('click', openDistributionModal);
      document.getElementById('edit-data-btn').addEventListener('click', openModal);
      document.getElementById('close-modal-btn').addEventListener('click', closeModal);
      document.getElementById('cancel-modal-btn').addEventListener('click', closeModal);
      document.getElementById('save-modal-btn').addEventListener('click', saveModalChanges);
      document.getElementById('reset-data-btn').addEventListener('click', resetAllData);
      document.getElementById('close-distribution-btn').addEventListener('click', closeDistributionModal);
      document.getElementById('cancel-distribution-btn').addEventListener('click', closeDistributionModal);
      document.getElementById('apply-distribution-btn').addEventListener('click', applyDistribution);
    }

    async function resetAllData() {
      const resetBtn = document.getElementById('reset-data-btn');
      const originalText = resetBtn.innerHTML;
      
      resetBtn.innerHTML = '⚠️ Confirmar?';
      resetBtn.style.backgroundColor = '#fef2f2';
      
      const confirmReset = () => {
        return new Promise((resolve) => {
          const handleClick = () => {
            resetBtn.removeEventListener('click', handleClick);
            resolve(true);
          };
          
          const handleCancel = () => {
            resetBtn.removeEventListener('click', handleClick);
            document.removeEventListener('click', handleOutsideClick);
            resolve(false);
          };
          
          const handleOutsideClick = (e) => {
            if (e.target !== resetBtn) {
              handleCancel();
            }
          };
          
          resetBtn.addEventListener('click', handleClick);
          setTimeout(() => {
            document.addEventListener('click', handleOutsideClick);
          }, 100);
          
          setTimeout(() => {
            handleCancel();
          }, 3000);
        });
      };
      
      const confirmed = await confirmReset();
      
      if (!confirmed) {
        resetBtn.innerHTML = originalText;
        resetBtn.style.backgroundColor = '';
        return;
      }
      
      resetBtn.innerHTML = '🔄 Resetando...';
      resetBtn.disabled = true;
      
      workingData = baseRawData.map((row, idx) => ({
        id: idx,
        window: row.window,
        hourLabel: row.hourLabel,
        capacity: row.capacity,
        billed: 0,
        loss: 0,
        lossType: "Sem perda"
      }));
      
      if (dataSdkInitialized && window.dataSdk) {
        const recordsToDelete = workingData.filter(row => row.__backendId);
        
        for (const row of recordsToDelete) {
          const result = await window.dataSdk.delete({ 
            hour_label: row.hourLabel,
            capacity: row.capacity,
            billed: row.billed,
            loss: row.loss,
            loss_type: row.lossType,
            __backendId: row.__backendId 
          });
          
          if (result.isError) {
            console.error("Erro ao deletar:", result.error);
          } else {
            delete row.__backendId;
          }
        }
      }
      
      updateLastUpdateTime();
      updateKpis();
      renderCurrentHourCard();
      renderTable();
      createLineChart();
      createBarChart();
      
      resetBtn.innerHTML = '✅ Resetado!';
      setTimeout(() => {
        resetBtn.innerHTML = originalText;
        resetBtn.disabled = false;
        resetBtn.style.backgroundColor = '';
      }, 1500);
    }

    async function initDataSdk() {
      if (!window.dataSdk) {
        console.log("Data SDK não disponível");
        return;
      }

      const dataHandler = {
        onDataChanged(data) {
          if (!data || data.length === 0) {
            return;
          }

          data.forEach(savedRow => {
            const matchingRow = workingData.find(r => r.hourLabel === savedRow.hour_label);
            if (matchingRow) {
              matchingRow.capacity = Number(savedRow.capacity || 0);
              matchingRow.billed = Number(savedRow.billed || 0);
              matchingRow.loss = Number(savedRow.loss || 0);
              matchingRow.lossType = savedRow.loss_type || "Sem perda";
              matchingRow.__backendId = savedRow.__backendId;
            }
          });

          updateKpis();
          renderCurrentHourCard();
          renderTable();
          createLineChart();
          createBarChart();
        }
      };

      const initResult = await window.dataSdk.init(dataHandler);
      if (initResult.isOk) {
        dataSdkInitialized = true;
        console.log("Data SDK inicializado com sucesso");
      } else {
        console.error("Erro ao inicializar Data SDK:", initResult.error);
      }
    }

    function applyConfigToUI(config) {
      const cfg = config || defaultConfig;
      const root = document.getElementById('app-root');
      const mainTitle = document.getElementById('main-title');
      const subtitleText = document.getElementById('subtitle-text');

      root.style.backgroundColor = cfg.background_color || defaultConfig.background_color;

      mainTitle.textContent = cfg.main_title || defaultConfig.main_title;
      subtitleText.textContent = cfg.subtitle || defaultConfig.subtitle;
    }

    (function initElementSdk() {
      if (!window.elementSdk) {
        applyConfigToUI(defaultConfig);
        return;
      }

      window.elementSdk.init({
        defaultConfig,
        onConfigChange: async (config) => {
          applyConfigToUI(config);
        },
        mapToCapabilities: (config) => {
          const cfg = config || defaultConfig;
          return {
            recolorables: [
              {
                get: () => cfg.background_color || defaultConfig.background_color,
                set: (value) => {
                  cfg.background_color = value;
                  window.elementSdk.setConfig({ background_color: value });
                }
              },
              {
                get: () => cfg.surface_color || defaultConfig.surface_color,
                set: (value) => {
                  cfg.surface_color = value;
                  window.elementSdk.setConfig({ surface_color: value });
                }
              },
              {
                get: () => cfg.text_color || defaultConfig.text_color,
                set: (value) => {
                  cfg.text_color = value;
                  window.elementSdk.setConfig({ text_color: value });
                }
              },
              {
                get: () => cfg.primary_action_color || defaultConfig.primary_action_color,
                set: (value) => {
                  cfg.primary_action_color = value;
                  window.elementSdk.setConfig({ primary_action_color: value });
                }
              },
              {
                get: () => cfg.secondary_action_color || defaultConfig.secondary_action_color,
                set: (value) => {
                  cfg.secondary_action_color = value;
                  window.elementSdk.setConfig({ secondary_action_color: value });
                }
              }
            ],
            borderables: [],
            fontEditable: {
              get: () => cfg.font_family || defaultConfig.font_family,
              set: (value) => {
                cfg.font_family = value;
                window.elementSdk.setConfig({ font_family: value });
              }
            },
            fontSizeable: {
              get: () => cfg.font_size || defaultConfig.font_size,
              set: (value) => {
                cfg.font_size = value;
                window.elementSdk.setConfig({ font_size: value });
              }
            }
          };
        },
        mapToEditPanelValues: (config) => {
          const map = new Map();
          const cfg = config || defaultConfig;
          map.set("main_title", cfg.main_title || defaultConfig.main_title);
          map.set("subtitle", cfg.subtitle || defaultConfig.subtitle);
          return map;
        }
      });
    })();

    async function initApp() {
      initHourSelects();
      initFilterEvents();
      
      await initDataSdk();
      
      updateKpis();
      renderCurrentHourCard();
      renderTable();
      createLineChart();
      createBarChart();
      applyConfigToUI(window.elementSdk ? window.elementSdk.config || defaultConfig : defaultConfig);

      setInterval(() => {
        updateCurrentTimeDisplay();
        renderCurrentHourCard();
      }, 1000);

      let resizeTimeout;
      window.addEventListener('resize', () => {
        clearTimeout(resizeTimeout);
        resizeTimeout = setTimeout(() => {
          createLineChart();
          createBarChart();
        }, 150);
      });
    }

    if (document.readyState === "loading") {
      document.addEventListener("DOMContentLoaded", initApp);
    } else {
      initApp();
    }
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9a8a7698515ff1f2',t:'MTc2NDg0MjQ5NC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
