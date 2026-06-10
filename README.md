<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>物理電路實驗室：串聯與並聯教學網頁 (AI 智慧版)</title>
    <!-- Tailwind CSS for modern responsive styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome for icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        /* 自訂畫布網格背景 */
        .grid-bg {
            background-size: 20px 20px;
            background-image: radial-gradient(circle, #cbd5e1 1px, transparent 1px);
        }
        /* 選中狀態與導線連接點脈衝效果 */
        @keyframes pulse-ring {
            0% { transform: scale(0.8); opacity: 0.5; }
            50% { transform: scale(1.3); opacity: 1; }
            100% { transform: scale(0.8); opacity: 0.5; }
        }
        .conn-point:hover {
            transform: scale(1.4);
            background-color: #ef4444;
        }
        /* 滾動條樣式優化 */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 3px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
    </style>
</head>
<body class="bg-slate-50 min-h-screen flex flex-col font-sans select-none">

    <!-- 頂部導航欄 -->
    <header class="bg-gradient-to-r from-blue-700 to-indigo-800 text-white shadow-md p-4">
        <div class="container mx-auto flex flex-col md:flex-row justify-between items-center gap-4">
            <div class="flex items-center gap-3">
                <div class="bg-yellow-400 text-slate-900 p-2 rounded-lg font-bold text-xl shadow animate-bounce">
                    <i class="fa-solid fa-bolt-lightning"></i>
                </div>
                <div>
                    <h1 class="text-2xl font-black tracking-wider flex items-center gap-2">
                        物理電路虛擬實驗室 <span class="bg-indigo-600 text-xs text-yellow-300 font-bold px-2 py-1 rounded-full border border-indigo-400">✨ Gemini AI 導師版</span>
                    </h1>
                    <p class="text-xs text-blue-100">結合生成式 AI 的探究式科學學習平台</p>
                </div>
            </div>
            <div class="flex items-center gap-4">
                <button onclick="showHelpModal()" class="bg-blue-600/50 hover:bg-blue-600 px-4 py-2 rounded-lg text-sm transition font-semibold flex items-center gap-2">
                    <i class="fa-solid fa-circle-question"></i> 使用說明
                </button>
                <div class="bg-indigo-900/60 px-4 py-2 rounded-lg text-sm border border-indigo-500/30">
                    <span id="score-display" class="font-bold text-yellow-400">得分: 0</span> 分
                </div>
            </div>
        </div>
    </header>

    <!-- 主內容區 -->
    <main class="flex-1 container mx-auto p-4 flex flex-col gap-6">
        
        <div class="flex flex-col lg:flex-row gap-6">
            <!-- 左側：謎題、元件庫與 AI 智慧助教 -->
            <div class="w-full lg:w-1/3 flex flex-col gap-6">
                
                <!-- 任務與謎題面板 -->
                <div class="bg-white p-5 rounded-2xl shadow-lg border border-slate-200">
                    <div class="flex justify-between items-center mb-3">
                        <h2 class="text-lg font-bold text-slate-800 flex items-center gap-2">
                            <i class="fa-solid fa-trophy text-amber-500"></i> 當前謎題挑戰
                        </h2>
                        <button onclick="generateNewPuzzle(true)" class="text-xs text-blue-600 hover:text-blue-800 font-semibold flex items-center gap-1">
                            <i class="fa-solid fa-rotate"></i> 換一題
                        </button>
                    </div>
                    
                    <div class="bg-blue-50 border-l-4 border-blue-500 p-4 rounded-r-lg mb-4">
                        <p id="puzzle-desc" class="text-sm text-slate-700 leading-relaxed font-medium">
                            載入謎題中...
                        </p>
                    </div>

                    <div class="flex justify-between items-center text-xs text-slate-500 bg-slate-50 p-2 rounded border border-slate-100">
                        <span>挑戰類型: <strong id="puzzle-type" class="text-slate-700">-</strong></span>
                        <span>目標要求: <strong id="puzzle-target" class="text-blue-600 font-bold">-</strong></span>
                    </div>
                </div>

                <!-- AI 智慧助教面板 (LLM Integration) -->
                <div class="bg-gradient-to-br from-indigo-900 to-slate-900 text-white p-5 rounded-2xl shadow-xl border border-indigo-700 flex flex-col gap-4">
                    <div class="flex justify-between items-center">
                        <h2 class="text-md font-bold flex items-center gap-2 text-indigo-300">
                            <i class="fa-solid fa-brain text-yellow-400 animate-pulse"></i> ✨ AI 物理智慧助教
                        </h2>
                        <span class="text-[10px] bg-indigo-500/30 text-indigo-200 border border-indigo-400/30 px-2 py-0.5 rounded-full">Gemini 2.5 Flash</span>
                    </div>

                    <!-- 智慧助教按鈕群 -->
                    <div class="grid grid-cols-2 gap-2">
                        <button onclick="askAIForHint()" class="p-2.5 bg-indigo-700 hover:bg-indigo-600 active:bg-indigo-800 rounded-xl text-xs font-bold transition flex items-center justify-center gap-1.5 shadow-md border border-indigo-500/50">
                            <i class="fa-solid fa-wand-magic-sparkles text-yellow-300"></i> ✨ 請求 AI 提示
                        </button>
                        <button onclick="explainCircuitWithAI()" class="p-2.5 bg-indigo-700 hover:bg-indigo-600 active:bg-indigo-800 rounded-xl text-xs font-bold transition flex items-center justify-center gap-1.5 shadow-md border border-indigo-500/50">
                            <i class="fa-solid fa-magnifying-glass-chart text-sky-300"></i> ✨ AI 電路分析
                        </button>
                    </div>

                    <!-- 自由提問箱 -->
                    <div class="border-t border-indigo-800/80 pt-3">
                        <p class="text-[11px] text-slate-400 mb-2">向 AI 老師自由提問（可結合當前電路狀態）：</p>
                        <div class="flex gap-2">
                            <input id="ai-question-input" type="text" placeholder="例如：為什麼我的燈泡不會亮？" class="flex-1 bg-slate-800 border border-indigo-500/30 text-white text-xs rounded-lg px-2.5 py-1.5 focus:outline-none focus:border-indigo-400">
                            <button onclick="askAIQuestion()" class="px-3 py-1.5 bg-emerald-600 hover:bg-emerald-500 active:bg-emerald-700 rounded-lg text-xs font-bold transition flex items-center gap-1">
                                ✨ 提問
                            </button>
                        </div>
                    </div>

                    <!-- AI 回覆顯示區域 -->
                    <div class="bg-slate-950/80 rounded-xl p-3 border border-indigo-950 min-h-[110px] max-h-[180px] overflow-y-auto flex flex-col justify-between">
                        <div id="ai-response-container" class="text-xs text-slate-300 leading-relaxed font-normal">
                            <p class="text-slate-500 italic text-center py-6">我是你的電學助教！點擊上方按鈕，我會針對你當前連接的電路給予啟發式教學提示、或是深度 analysis 喔！你也可以在提問箱內隨時向我提問物理問題！</p>
                        </div>
                        <!-- 載入中動畫 -->
                        <div id="ai-loader" class="hidden py-4 flex flex-col items-center justify-center gap-2">
                            <div class="flex space-x-1.5">
                                <div class="w-2.5 h-2.5 bg-yellow-400 rounded-full animate-bounce" style="animation-delay: 0.1s"></div>
                                <div class="w-2.5 h-2.5 bg-indigo-400 rounded-full animate-bounce" style="animation-delay: 0.2s"></div>
                                <div class="w-2.5 h-2.5 bg-purple-400 rounded-full animate-bounce" style="animation-delay: 0.3s"></div>
                            </div>
                            <span class="text-[10px] text-slate-400">AI 老師正在思考解答中...</span>
                        </div>
                    </div>
                </div>

                <!-- 元件庫面板 -->
                <div class="bg-white p-5 rounded-2xl shadow-lg border border-slate-200 flex-1">
                    <h2 class="text-lg font-bold text-slate-800 mb-4 flex items-center gap-2">
                        <i class="fa-solid fa-box-open text-indigo-500"></i> 元件工具箱
                    </h2>
                    <p class="text-xs text-slate-400 mb-4">請點擊元件，將其新增至工作區。按【R】鍵或右鍵可旋轉，拖拽端點可拉線。</p>
                    
                    <!-- 元件滾動列表 -->
                    <div class="grid grid-cols-2 gap-3 max-h-[260px] overflow-y-auto pr-1">
                        <!-- 電池 -->
                        <button onclick="spawnComponent('battery')" class="flex flex-col items-center justify-between p-3 bg-slate-50 hover:bg-indigo-50 rounded-xl border border-slate-200 hover:border-indigo-400 transition group text-center h-28">
                            <svg class="w-16 h-10 text-slate-700 group-hover:text-indigo-600" viewBox="0 0 80 40">
                                <!-- 電池符號 -->
                                <line x1="10" y1="20" x2="35" y2="20" stroke="currentColor" stroke-width="2"/>
                                <line x1="35" y1="10" x2="35" y2="30" stroke="currentColor" stroke-width="4"/>
                                <line x1="45" y1="15" x2="45" y2="25" stroke="currentColor" stroke-width="2"/>
                                <line x1="45" y1="20" x2="70" y2="20" stroke="currentColor" stroke-width="2"/>
                                <text x="25" y="12" font-size="10" fill="currentColor">+</text>
                            </svg>
                            <span class="text-xs font-semibold text-slate-700">單電池 (1.5V)</span>
                        </button>

                        <!-- 電池組 -->
                        <button onclick="spawnComponent('battery_pack')" class="flex flex-col items-center justify-between p-3 bg-slate-50 hover:bg-indigo-50 rounded-xl border border-slate-200 hover:border-indigo-400 transition group text-center h-28">
                            <svg class="w-16 h-10 text-slate-700 group-hover:text-indigo-600" viewBox="0 0 80 40">
                                <!-- 電池組符號 -->
                                <line x1="10" y1="20" x2="25" y2="20" stroke="currentColor" stroke-width="2"/>
                                <line x1="25" y1="10" x2="25" y2="30" stroke="currentColor" stroke-width="4"/>
                                <line x1="32" y1="15" x2="32" y2="25" stroke="currentColor" stroke-width="2"/>
                                <line x1="39" y1="10" x2="39" y2="30" stroke="currentColor" stroke-width="4"/>
                                <line x1="46" y1="15" x2="46" y2="25" stroke="currentColor" stroke-width="2"/>
                                <line x1="53" y1="10" x2="53" y2="30" stroke="currentColor" stroke-width="4"/>
                                <line x1="60" y1="15" x2="60" y2="25" stroke="currentColor" stroke-width="2"/>
                                <line x1="60" y1="20" x2="70" y2="20" stroke="currentColor" stroke-width="2"/>
                            </svg>
                            <span class="text-xs font-semibold text-slate-700">電池組 (6.0V)</span>
                        </button>

                        <!-- 燈泡 -->
                        <button onclick="spawnComponent('bulb')" class="flex flex-col items-center justify-between p-3 bg-slate-50 hover:bg-indigo-50 rounded-xl border border-slate-200 hover:border-indigo-400 transition group text-center h-28">
                            <svg class="w-16 h-10 text-slate-700 group-hover:text-indigo-600" viewBox="0 0 80 40">
                                <line x1="10" y1="20" x2="30" y2="20" stroke="currentColor" stroke-width="2"/>
                                <circle cx="40" cy="20" r="10" fill="none" stroke="currentColor" stroke-width="2"/>
                                <line x1="33" y1="13" x2="47" y2="27" stroke="currentColor" stroke-width="2"/>
                                <line x1="47" y1="13" x2="33" y2="27" stroke="currentColor" stroke-width="2"/>
                                <line x1="50" y1="20" x2="80" y2="20" stroke="currentColor" stroke-width="2"/>
                            </svg>
                            <span class="text-xs font-semibold text-slate-700">燈泡 (10Ω)</span>
                        </button>

                        <!-- 開關 -->
                        <button onclick="spawnComponent('switch')" class="flex flex-col items-center justify-between p-3 bg-slate-50 hover:bg-indigo-50 rounded-xl border border-slate-200 hover:border-indigo-400 transition group text-center h-28">
                            <svg class="w-16 h-10 text-slate-700 group-hover:text-indigo-600" viewBox="0 0 80 40">
                                <line x1="10" y1="20" x2="30" y2="20" stroke="currentColor" stroke-width="2"/>
                                <circle cx="30" cy="20" r="2" fill="currentColor"/>
                                <line x1="30" y1="20" x2="50" y2="10" stroke="currentColor" stroke-width="2"/>
                                <circle cx="50" cy="20" r="2" fill="currentColor"/>
                                <line x1="50" y1="20" x2="80" y2="20" stroke="currentColor" stroke-width="2"/>
                            </svg>
                            <span class="text-xs font-semibold text-slate-700">單刀開關</span>
                        </button>

                        <!-- 電阻器 -->
                        <button onclick="spawnComponent('resistor')" class="flex flex-col items-center justify-between p-3 bg-slate-50 hover:bg-indigo-50 rounded-xl border border-slate-200 hover:border-indigo-400 transition group text-center h-28">
                            <svg class="w-16 h-10 text-slate-700 group-hover:text-indigo-600" viewBox="0 0 80 40">
                                <line x1="10" y1="20" x2="25" y2="20" stroke="currentColor" stroke-width="2"/>
                                <rect x="25" y="14" width="30" height="12" fill="none" stroke="currentColor" stroke-width="2"/>
                                <line x1="55" y1="20" x2="80" y2="20" stroke="currentColor" stroke-width="2"/>
                            </svg>
                            <span class="text-xs font-semibold text-slate-700">定值電阻 (5Ω)</span>
                        </button>

                        <!-- 變阻器 -->
                        <button onclick="spawnComponent('rheostat')" class="flex flex-col items-center justify-between p-3 bg-slate-50 hover:bg-indigo-50 rounded-xl border border-slate-200 hover:border-indigo-400 transition group text-center h-28">
                            <svg class="w-16 h-10 text-slate-700 group-hover:text-indigo-600" viewBox="0 0 80 40">
                                <line x1="10" y1="20" x2="25" y2="20" stroke="currentColor" stroke-width="2"/>
                                <rect x="25" y="14" width="30" height="12" fill="none" stroke="currentColor" stroke-width="2"/>
                                <line x1="55" y1="20" x2="80" y2="20" stroke="currentColor" stroke-width="2"/>
                                <!-- 變阻器箭頭 -->
                                <line x1="20" y1="32" x2="55" y2="8" stroke="currentColor" stroke-width="1.5"/>
                                <polygon points="55,8 51,12 55,12" fill="currentColor" stroke="currentColor" stroke-width="1"/>
                            </svg>
                            <span class="text-xs font-semibold text-slate-700">滑動變阻器</span>
                        </button>

                        <!-- 安培計 -->
                        <button onclick="spawnComponent('ammeter')" class="flex flex-col items-center justify-between p-3 bg-slate-50 hover:bg-indigo-50 rounded-xl border border-slate-200 hover:border-indigo-400 transition group text-center h-28">
                            <svg class="w-16 h-10 text-slate-700 group-hover:text-indigo-600" viewBox="0 0 80 40">
                                <line x1="10" y1="20" x2="25" y2="20" stroke="currentColor" stroke-width="2"/>
                                <circle cx="40" cy="20" r="15" fill="none" stroke="currentColor" stroke-width="2"/>
                                <text x="40" y="25" font-size="14" font-weight="bold" text-anchor="middle" fill="currentColor">A</text>
                                <line x1="55" y1="20" x2="70" y2="20" stroke="currentColor" stroke-width="2"/>
                            </svg>
                            <span class="text-xs font-semibold text-slate-700">安培計</span>
                        </button>

                        <!-- 伏特計 -->
                        <button onclick="spawnComponent('voltmeter')" class="flex flex-col items-center justify-between p-3 bg-slate-50 hover:bg-indigo-50 rounded-xl border border-slate-200 hover:border-indigo-400 transition group text-center h-28">
                            <svg class="w-16 h-10 text-slate-700 group-hover:text-indigo-600" viewBox="0 0 80 40">
                                <line x1="10" y1="20" x2="25" y2="20" stroke="currentColor" stroke-width="2"/>
                                <circle cx="40" cy="20" r="15" fill="none" stroke="currentColor" stroke-width="2"/>
                                <text x="40" y="25" font-size="14" font-weight="bold" text-anchor="middle" fill="currentColor">V</text>
                                <line x1="55" y1="20" x2="70" y2="20" stroke="currentColor" stroke-width="2"/>
                            </svg>
                            <span class="text-xs font-semibold text-slate-700">伏特計</span>
                        </button>
                    </div>
                </div>
            </div>

            <!-- 右側：工作畫布區 -->
            <div class="w-full lg:w-2/3 flex flex-col gap-4">
                
                <!-- 控制按鈕 & 工作區標籤 -->
                <div class="flex flex-wrap justify-between items-center gap-3 bg-white p-3 rounded-xl shadow-md border border-slate-200">
                    <div class="flex gap-2">
                        <button id="tab-series" onclick="switchTab('series')" class="px-5 py-2 rounded-lg font-bold text-sm transition-all shadow-sm bg-blue-600 text-white">
                            <i class="fa-solid fa-square-minus mr-1"></i> 串聯電路畫布
                        </button>
                        <button id="tab-parallel" onclick="switchTab('parallel')" class="px-5 py-2 rounded-lg font-bold text-sm transition-all hover:bg-slate-100 text-slate-600">
                            <i class="fa-solid fa-grip mr-1"></i> 並聯電路畫布
                        </button>
                    </div>
                    
                    <!-- 控制與刪除按鈕組 -->
                    <div class="flex flex-wrap gap-2">
                        <!-- 刪除選中元件按鈕 -->
                        <button id="delete-comp-btn" onclick="deleteSelectedComponentOnly()" class="px-3 py-2 bg-slate-100 text-slate-400 cursor-not-allowed rounded-lg text-sm font-bold transition flex items-center gap-1" disabled>
                            <i class="fa-solid fa-trash-can"></i> 刪除選中元件
                        </button>
                        <!-- 刪除選中線路按鈕 -->
                        <button id="delete-wire-btn" onclick="deleteSelectedWireOnly()" class="px-3 py-2 bg-slate-100 text-slate-400 cursor-not-allowed rounded-lg text-sm font-bold transition flex items-center gap-1" disabled>
                            <i class="fa-solid fa-scissors"></i> 刪除選中導線
                        </button>
                        <!-- 其他控制功能 -->
                        <button onclick="clearCanvas()" class="px-4 py-2 bg-rose-100 hover:bg-rose-200 text-rose-700 rounded-lg text-sm font-bold transition flex items-center gap-1">
                            <i class="fa-solid fa-rotate-left"></i> 清空電路
                        </button>
                        <button id="simulate-btn" onclick="toggleSimulation()" class="px-5 py-2 bg-amber-500 hover:bg-amber-600 text-white rounded-lg text-sm font-bold transition flex items-center gap-1 shadow">
                            <i class="fa-solid fa-power-off"></i> 通電閉合
                        </button>
                        <button onclick="verifyAnswer()" class="px-6 py-2 bg-emerald-600 hover:bg-emerald-700 text-white rounded-lg text-sm font-extrabold transition flex items-center gap-1 shadow-lg">
                            <i class="fa-solid fa-circle-check"></i> 驗證答案
                        </button>
                    </div>
                </div>

                <!-- 主畫布畫布區 -->
                <div id="canvas-wrapper" class="relative w-full h-[550px] bg-white rounded-2xl shadow-xl border-2 border-slate-200 overflow-hidden grid-bg flex flex-col">
                    
                    <!-- 畫布通知與儀表板 -->
                    <div class="absolute top-3 left-3 right-3 z-10 flex justify-between items-center pointer-events-none">
                        <div class="bg-slate-900/80 text-white text-xs px-3 py-1.5 rounded-full flex items-center gap-2 backdrop-blur">
                            <span class="w-2.5 h-2.5 rounded-full bg-emerald-500 inline-block animate-ping"></span>
                            當前系統狀態：<span id="status-text" class="font-semibold text-emerald-400">斷路</span>
                        </div>
                        
                        <div id="sim-dashboard" class="hidden bg-slate-900/95 text-white p-3 rounded-xl border border-slate-700/50 flex gap-4 text-xs shadow-xl backdrop-blur pointer-events-auto">
                            <div>電流強度: <span id="dash-current" class="text-amber-400 font-bold">0.00 A</span></div>
                            <div class="border-l border-slate-700 pl-4">迴路總電阻: <span id="dash-res" class="text-sky-400 font-bold">0.0 Ω</span></div>
                            <div class="border-l border-slate-700 pl-4">變阻器當前阻值: <span id="dash-rheo" class="text-emerald-400 font-bold">5.0 Ω</span></div>
                        </div>
                    </div>

                    <!-- 導線/連線 SVG 層 (提高 z-index，設定 pointer-events-none，透過 path 各自開啟點擊) -->
                    <svg id="wire-svg" class="absolute inset-0 w-full h-full pointer-events-none z-10">
                        <g id="wires-group"></g>
                        <path id="temp-wire" d="" stroke="#ef4444" stroke-width="3" stroke-dasharray="5,5" fill="none" class="hidden"/>
                    </svg>

                    <!-- 元件容器 (改為 pointer-events-none 避免大區塊擋住點擊) -->
                    <div id="canvas-container" class="absolute inset-0 z-20 pointer-events-none">
                        <!-- 元件會在這裡動態新增 -->
                    </div>

                    <!-- 空畫布引導 -->
                    <div id="empty-canvas-guide" class="absolute inset-0 flex flex-col items-center justify-center text-slate-400 pointer-events-none">
                        <i class="fa-solid fa-screwdriver-wrench text-5xl mb-3 text-slate-300"></i>
                        <p class="font-medium">您的電路畫布還是空的</p>
                        <p class="text-xs text-slate-400 mt-1">點選左側元件、拖曳定位、連接端點，點擊【通電】觀察變化</p>
                    </div>
                </div>

                <!-- 元件操作提示 -->
                <div class="bg-slate-100 p-4 rounded-xl border border-slate-200 text-xs text-slate-600 flex flex-wrap justify-between items-center gap-2">
                    <span class="flex items-center gap-1.5"><kbd class="bg-white px-2 py-1 rounded shadow text-slate-800 font-bold">滑鼠左鍵</kbd> 選中/拖曳元件</span>
                    <span class="flex items-center gap-1.5"><kbd class="bg-white px-2 py-1 rounded shadow text-slate-800 font-bold">R</kbd> 或 <kbd class="bg-white px-2 py-1 rounded shadow text-slate-800 font-bold">滑鼠右鍵</kbd> 旋轉元件 90°</span>
                    <span class="flex items-center gap-1.5"><kbd class="bg-white px-2 py-1 rounded shadow text-slate-800 font-bold">Delete</kbd> 快速刪除選中元件或導線</span>
                    <span class="flex items-center gap-1.5 text-blue-600"><i class="fa-solid fa-lightbulb"></i> 小技巧：雙擊開關可手動手動切換開關狀態！</span>
                </div>
            </div>
        </div>

        <!-- 答對挑戰記錄面板 (底欄新增) -->
        <div class="bg-white p-5 rounded-2xl shadow-lg border border-slate-200 w-full mt-2">
            <div class="flex justify-between items-center mb-4 pb-2 border-b border-slate-100">
                <h3 class="text-md font-bold text-slate-800 flex items-center gap-2">
                    <i class="fa-solid fa-circle-check text-emerald-500 animate-pulse"></i> ✨ 已答對挑戰成就榜
                </h3>
                <button onclick="resetSolvedPuzzles()" class="text-xs text-rose-600 hover:text-rose-800 font-bold flex items-center gap-1.5 bg-rose-50 px-3 py-1.5 rounded-lg border border-rose-100 transition hover:bg-rose-100">
                    <i class="fa-solid fa-trash-can"></i> 重設所有記錄
                </button>
            </div>
            <div id="solved-records-container" class="flex flex-wrap gap-2.5 min-h-[50px] items-center">
                <p class="text-xs text-slate-400 italic">目前尚未有答對的記錄。動動手，開啟通電並點擊「驗證答案」來點亮你的第一個成就徽章吧！</p>
            </div>
        </div>
        
    </main>

    <!-- 成功與反饋對話框 (Modal) -->
    <div id="success-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm flex items-center justify-center z-50 hidden p-4">
        <div class="bg-white w-full max-w-md rounded-2xl shadow-2xl p-6 text-center border-t-8 border-emerald-500 scale-95 transition-all">
            <div class="w-16 h-16 bg-emerald-100 text-emerald-600 rounded-full flex items-center justify-center mx-auto mb-4 text-3xl shadow">
                <i class="fa-solid fa-circle-check"></i>
            </div>
            <h3 class="text-xl font-bold text-slate-800 mb-2">挑戰成功！太棒了！</h3>
            <p id="success-message" class="text-sm text-slate-600 mb-6">電路完美契合物理邏輯！你對串並聯電路的分壓和阻值變化掌握得很準確！</p>
            <div class="flex gap-3 justify-center">
                <button onclick="closeSuccessModal()" class="px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 font-bold text-sm rounded-lg transition">
                    留在本頁
                </button>
                <button onclick="nextPuzzleAndClose()" class="px-5 py-2 bg-emerald-600 hover:bg-emerald-700 text-white font-bold text-sm rounded-lg transition shadow-md">
                    挑戰下一題
                </button>
            </div>
        </div>
    </div>

    <!-- 說明與幫助對話框 -->
    <div id="help-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm flex items-center justify-center z-50 hidden p-4">
        <div class="bg-white w-full max-w-lg rounded-2xl shadow-2xl p-6 border-t-8 border-indigo-600 max-h-[85vh] overflow-y-auto">
            <h3 class="text-xl font-bold text-slate-800 mb-4 flex items-center gap-2">
                <i class="fa-solid fa-compass text-indigo-600"></i> 如何使用此模擬電路實驗室？
            </h3>
            
            <div class="space-y-4 text-sm text-slate-600">
                <div>
                    <h4 class="font-bold text-slate-800 mb-1">1. 新增元件與佈局</h4>
                    <p>在左側點選您想要的元件。新增後，滑鼠拖曳元件可以任意擺放位置。按下鍵盤 <kbd class="bg-slate-100 px-1 py-0.5 border rounded">R</kbd> 鍵、或在元件上點選「右鍵」可以將其旋轉90度。</p>
                </div>
                <div>
                    <h4 class="font-bold text-slate-800 mb-1">2. 接線（連接電路）</h4>
                    <p>每個元件都有紅色的**端點圈圈**。在一個端點上點選並按住不放，拖拽出紅色虛線，移到另一個元件的端點並放開，即可完成接線。</p>
                </div>
                <div>
                    <h4 class="font-bold text-slate-800 mb-1">3. 刪除與清理</h4>
                    <p>您可以使用上方控制面板中獨立的**「刪除選中元件」**或**「刪除選中導線」**按鈕來快速移除不需要的部分。也可以選中元件/導線後直接按鍵盤의 <kbd class="bg-slate-100 px-1 py-0.5 border rounded">Delete</kbd> 鍵。</p>
                </div>
                <div>
                    <h4 class="font-bold text-slate-800 mb-1">4. 閉合電路與滑動變阻器</h4>
                    <p>點擊「通電閉合」後，電路若通路，電流、電壓指示會顯示出來，燈泡也會發光。如果使用滑動變阻器，可以在畫布上選中變阻器，利用上方出現的滑動桿調整阻值，直接觀察安培計與伏特計的數值變化！</p>
                </div>
            </div>

            <div class="mt-6 flex justify-end">
                <button onclick="closeHelpModal()" class="px-5 py-2 bg-indigo-600 hover:bg-indigo-700 text-white font-bold text-sm rounded-lg transition shadow-md">
                    我明白了
                </button>
            </div>
        </div>
    </div>

    <!-- 系統提示橫幅 (用來取代 alert) -->
    <div id="toast" class="fixed bottom-6 right-6 z-50 bg-slate-800 text-white px-4 py-3 rounded-xl shadow-2xl flex items-center gap-3 transition-all transform translate-y-20 opacity-0 pointer-events-none">
        <i id="toast-icon" class="fa-solid fa-circle-exclamation text-amber-400"></i>
        <span id="toast-msg" class="text-sm font-semibold">提示訊息</span>
    </div>

    <!-- 元件 HTML 範本 (用於 JavaScript 動態生成) -->
    <script>
        // --- 核心變數與電路狀態 ---
        let score = 0;
        let currentTab = 'series'; // 'series' (串聯) or 'parallel' (並聯)
        let isSimulating = false;

        // 答對記錄狀態陣列
        let solvedPuzzleIds = [];

        // 電路資料結構
        const circuitState = {
            series: {
                components: [], // { id, type, x, y, rotation, value, state: {} }
                wires: []       // { id, fromCompId, fromTerminal, toCompId, toTerminal }
            },
            parallel: {
                components: [],
                wires: []
            }
        };

        // 元件庫屬性定義
        const compTypes = {
            battery: { name: '單電池', value: 1.5, unit: 'V', res: 0.1, color: '#f59e0b' },
            battery_pack: { name: '電池組', value: 6.0, unit: 'V', res: 0.2, color: '#d97706' },
            bulb: { name: '燈泡', value: 10.0, unit: 'Ω', res: 10.0, color: '#eab308' },
            switch: { name: '單刀開關', value: 0, unit: '', res: 0.001, color: '#64748b' },
            resistor: { name: '定值電阻', value: 5.0, unit: 'Ω', res: 5.0, color: '#3b82f6' },
            rheostat: { name: '滑動變阻器', value: 5.0, unit: 'Ω', res: 5.0, maxRes: 20, color: '#10b981' },
            ammeter: { name: '安培計', value: 0.01, unit: 'Ω', res: 0.01, color: '#ec4899' },
            voltmeter: { name: '伏特計', value: 100000, unit: 'Ω', res: 100000, color: '#8b5cf6' }
        };

        // 當前互動編輯狀態
        let selectedElement = null; // 當前選中的元件或導線
        let dragTarget = null;      // 正在拖曳的元件
        let dragOffset = { x: 0, y: 0 };
        let activeWireStart = null; // 正在連線的起點資訊: { compId, termIdx, termX, termY }

        // 當前隨機產生的謎題
        let currentPuzzle = null;

        // ==========================================
        // ======= ✨ 10組 串並聯謎題數據庫 ========
        // ==========================================
        const puzzleDatabase = [
            // ------ 串聯電路題庫 (1 至 5) ------
            {
                id: 1,
                title: "串聯分壓挑戰",
                desc: "請在串聯畫布上，使用一個【電池組 (6V)】、一個【定值電阻 (5Ω)】與一個【燈泡 (10Ω)】串聯。此時燈泡兩端的電壓應該會正好是多少？",
                type: "串聯電路",
                target: "讓燈泡獲得 4.0V 的電壓",
                verify: (components, wires, calcResults) => {
                    const hasBattery = components.some(c => c.type === 'battery_pack');
                    const hasRes = components.some(c => c.type === 'resistor');
                    const hasBulb = components.some(c => c.type === 'bulb');
                    if (!hasBattery || !hasRes || !hasBulb) return { ok: false, msg: "必須同時包含一個【電池組】、【定值電阻】與【燈泡】！" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "電路尚未通電閉合！" };
                    if (!isPerfectSeries(components, wires)) return { ok: false, msg: "這不是一個完美的單一迴路串聯電路喔！" };

                    const bulbComp = components.find(c => c.type === 'bulb');
                    const bulbVolts = calcResults[bulbComp.id]?.volts || 0;
                    if (Math.abs(bulbVolts - 4.0) < 0.2) return { ok: true };
                    return { ok: false, msg: `燈泡目前獲得 ${bulbVolts.toFixed(1)}V 電壓，不符合目標 4.0V！` };
                }
            },
            {
                id: 2,
                title: "阻抗分壓測量",
                desc: "利用一個【單電池 (1.5V)】、一個【定值電阻 (5Ω)】、一個【燈泡 (10Ω)】串聯，並用【伏特計】並聯在【定值電阻】兩端來測量分壓。",
                type: "串聯電路",
                target: "用伏特計量出電阻分壓約 0.5V",
                verify: (components, wires, calcResults) => {
                    const hasRes = components.some(c => c.type === 'resistor');
                    const hasVM = components.some(c => c.type === 'voltmeter');
                    if (!hasRes || !hasVM) return { ok: false, msg: "需要放置定值電阻和伏特計！" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "請先閉合電路！" };

                    const resComp = components.find(c => c.type === 'resistor');
                    const vmComp = components.find(c => c.type === 'voltmeter');
                    if (!checkParallel(resComp.id, vmComp.id, wires)) return { ok: false, msg: "伏特計必須『並聯』連接在定值電阻的兩端！" };

                    const vmVolts = calcResults[vmComp.id]?.volts || 0;
                    if (Math.abs(vmVolts - 0.5) < 0.1) return { ok: true };
                    return { ok: false, msg: `伏特計讀數為 ${vmVolts.toFixed(2)}V，尚未達到目標分壓 0.5V。` };
                }
            },
            {
                id: 3,
                title: "變阻分壓調控",
                desc: "使用【電池組 (6V)】與【定值電阻 (5Ω)】和【滑動變阻器】串聯。藉由調整變阻器的阻值，來控制定值電阻兩端分得 2.0V 的電壓。",
                type: "串聯電路",
                target: "調節滑動變阻器使電阻分壓為 2.0V",
                verify: (components, wires, calcResults) => {
                    const hasRheo = components.some(c => c.type === 'rheostat');
                    const hasRes = components.some(c => c.type === 'resistor');
                    if (!hasRheo || !hasRes) return { ok: false, msg: "電路需包含定值電阻與滑動變阻器！" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "請先通電閉合電路！" };

                    const resComp = components.find(c => c.type === 'resistor');
                    const resVolts = calcResults[resComp.id]?.volts || 0;
                    if (Math.abs(resVolts - 2.0) < 0.1) return { ok: true };
                    return { ok: false, msg: `定值電阻目前電壓為 ${resVolts.toFixed(1)}V。請點選滑動變阻器，使用上方滑桿調整阻值（提示：調大阻值會降低電阻電壓）！` };
                }
            },
            {
                id: 4,
                title: "雙燈串聯阻抗與電流",
                desc: "將【單電池 (1.5V)】與【兩個燈泡 (每個 10Ω)】及一個【安培計】全部串聯在同一個迴路中。測量看看串聯兩個負載後，總電流會下降到多少？",
                type: "串聯電路",
                target: "使迴路總電流降至 0.075A 左右",
                verify: (components, wires, calcResults) => {
                    const bulbs = components.filter(c => c.type === 'bulb');
                    const hasAM = components.some(c => c.type === 'ammeter');
                    if (bulbs.length < 2 || !hasAM) return { ok: false, msg: "您需要至少【兩個燈泡】與一個【安培計】！" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "請先通電！" };

                    const amComp = components.find(c => c.type === 'ammeter');
                    const current = calcResults[amComp.id]?.current || 0;
                    if (Math.abs(current - 0.075) < 0.015) return { ok: true };
                    return { ok: false, msg: `目前電流為 ${current.toFixed(3)}A。請確保將兩個燈泡串聯起來，而非並聯！` };
                }
            },
            {
                id: 5,
                title: "安全開關控制分壓",
                desc: "使用【電池組 (6V)】、一個【單刀開關】與【兩個定值電阻 (每個 5Ω)】串聯。當開關閉合通電時，其中一個電阻的分壓應為 3.0V。",
                type: "串聯電路",
                target: "開關通電使其中一個定值電阻分壓為 3.0V",
                verify: (components, wires, calcResults) => {
                    const resistors = components.filter(c => c.type === 'resistor');
                    const sw = components.find(c => c.type === 'switch');
                    if (resistors.length < 2 || !sw) return { ok: false, msg: "必須放置兩個【定值電阻】與一個【單刀開關】！" };
                    if (sw.state.isOpen) return { ok: false, msg: "開關處於斷開狀態！請雙擊開關使其閉合通電。" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "電路未接通，請檢查接線！" };

                    const resVolts = calcResults[resistors[0].id]?.volts || 0;
                    if (Math.abs(resVolts - 3.0) < 0.2) return { ok: true };
                    return { ok: false, msg: `電阻電壓目前為 ${resVolts.toFixed(1)}V，不符合 3.0V。` };
                }
            },

            // ------ 並聯電路題庫 (6 至 10) ------
            {
                id: 6,
                title: "並聯總分流檢測",
                desc: "在並聯畫布上，使用【電池組 (6V)】為電源，『並聯』連接一個【定值電阻 (5Ω)】與一個【燈泡 (10Ω)】。請在『幹線（主迴路）』中串聯一個【安培計】測量總電流。",
                type: "並聯電路",
                target: "使幹線安培計讀數達到 1.8A",
                verify: (components, wires, calcResults) => {
                    const hasRes = components.some(c => c.type === 'resistor');
                    const hasBulb = components.some(c => c.type === 'bulb');
                    const hasAM = components.some(c => c.type === 'ammeter');
                    if (!hasRes || !hasBulb || !hasAM) return { ok: false, msg: "需要電阻、燈泡以及安培計！" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "電路尚未通電閉合！" };

                    const resComp = components.find(c => c.type === 'resistor');
                    const bulbComp = components.find(c => c.type === 'bulb');
                    if (!checkParallel(resComp.id, bulbComp.id, wires)) return { ok: false, msg: "定值電阻與燈泡必須互為『並聯』連接！" };

                    const amComp = components.find(c => c.type === 'ammeter');
                    const amCurrent = calcResults[amComp.id]?.current || 0;
                    if (Math.abs(amCurrent - 1.8) < 0.1) return { ok: true };
                    return { ok: false, msg: `安培計的讀數為 ${amCurrent.toFixed(2)}A。請確保安培計串聯在幹線上（測量總電流），而非單一支路！` };
                }
            },
            {
                id: 7,
                title: "並聯等電壓特性",
                desc: "並聯電路中各支路的電壓相等。請用【單電池 (1.5V)】，『並聯』連接【定值電阻 (5Ω)】與【燈泡 (10Ω)】。使用【伏特計】跨接測量【燈泡】兩端電壓。",
                type: "並聯電路",
                target: "用伏特計測量並聯燈泡電壓為 1.5V",
                verify: (components, wires, calcResults) => {
                    const hasBulb = components.some(c => c.type === 'bulb');
                    const hasVM = components.some(c => c.type === 'voltmeter');
                    if (!hasBulb || !hasVM) return { ok: false, msg: "必須包含燈泡和伏特計！" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "請先閉合電路！" };

                    const bulbComp = components.find(c => c.type === 'bulb');
                    const vmComp = components.find(c => c.type === 'voltmeter');
                    if (!checkParallel(bulbComp.id, vmComp.id, wires)) return { ok: false, msg: "伏特計必須『並聯』連接在燈泡兩端！" };

                    const vmVolts = calcResults[vmComp.id]?.volts || 0;
                    if (Math.abs(vmVolts - 1.5) < 0.1) return { ok: true };
                    return { ok: false, msg: `伏特計讀數為 ${vmVolts.toFixed(1)}V。請確認電阻和燈泡是否確實為並聯。` };
                }
            },
            {
                id: 8,
                title: "支路獨立開關控制",
                desc: "使用【電池組 (6V)】為電源，『並聯』連接【兩個燈泡 (每個 10Ω)】。在『其中一條燈泡支路』上串聯一個【單刀開關】。當開關閉合時，總電流應為 1.2A。",
                type: "並聯電路",
                target: "開關接通時主幹線總電流為 1.2A",
                verify: (components, wires, calcResults) => {
                    const bulbs = components.filter(c => c.type === 'bulb');
                    const sw = components.find(c => c.type === 'switch');
                    if (bulbs.length < 2 || !sw) return { ok: false, msg: "需要兩個燈泡與一個開關！" };
                    if (sw.state.isOpen) return { ok: false, msg: "開關目前處於斷開狀態！請雙擊開關使其通電。" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "電路未完全接通！" };

                    const battery = components.find(c => c.type === 'battery_pack');
                    const totalCurrent = calcResults[battery.id]?.current || 0;
                    if (Math.abs(totalCurrent - 1.2) < 0.15) return { ok: true };
                    return { ok: false, msg: `當前總電流為 ${totalCurrent.toFixed(2)}A。請檢查開關是否只控制了其中一個燈泡支路，且兩燈泡為並聯！` };
                }
            },
            {
                id: 9,
                title: "安培計支路分流檢測",
                desc: "使用【電池組 (6V)】電源，並聯連接一個【定值電阻 (5Ω)】與一個【燈泡 (10Ω)】。這次，請將【安培計】『只』與【燈泡】串聯在同一條支路上（測量分流）。",
                type: "並聯電路",
                target: "使支路安培計顯示燈泡分流為 0.6A",
                verify: (components, wires, calcResults) => {
                    const hasRes = components.some(c => c.type === 'resistor');
                    const hasBulb = components.some(c => c.type === 'bulb');
                    const hasAM = components.some(c => c.type === 'ammeter');
                    if (!hasRes || !hasBulb || !hasAM) return { ok: false, msg: "需要電阻、燈泡以及安培計！" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "電路未通電！" };

                    const amComp = components.find(c => c.type === 'ammeter');
                    const amCurrent = calcResults[amComp.id]?.current || 0;
                    
                    if (Math.abs(amCurrent - 0.6) < 0.08) return { ok: true };
                    return { ok: false, msg: `安培計當前讀數為 ${amCurrent.toFixed(2)}A。請把安培計移到『只有燈泡那條支路』上，不要放在主幹線上！` };
                }
            },
            {
                id: 10,
                title: "變阻並聯調節挑戰",
                desc: "使用【電池組 (6V)】，並聯連接【燈泡 (10Ω)】與【滑動變阻器】。在幹線上串聯【安培計】，調整滑動變阻器的阻值，直到總電流剛好為 1.8A。",
                type: "並聯電路",
                target: "調節變阻器使並聯總電流達到 1.8A",
                verify: (components, wires, calcResults) => {
                    const hasBulb = components.some(c => c.type === 'bulb');
                    const hasRheo = components.some(c => c.type === 'rheostat');
                    const hasAM = components.some(c => c.type === 'ammeter');
                    if (!hasBulb || !hasRheo || !hasAM) return { ok: false, msg: "需要燈泡、滑動變阻器與安培計！" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "電路尚未通電閉合！" };

                    const amComp = components.find(c => c.type === 'ammeter');
                    const current = calcResults[amComp.id]?.current || 0;
                    
                    if (Math.abs(current - 1.8) < 0.1) return { ok: true };
                    return { ok: false, msg: `主迴路總電流目前為 ${current.toFixed(2)}A。請選中滑動變阻器並拖動上方的阻值滑塊，使總電流達到 1.8A（提示：阻值調到 5.0Ω 左右）！` };
                }
            }
        ];

        // 輔助獲取特定元件ID
        function resCompId(comps) { return comps.find(c => c.type === 'resistor')?.id; }
        function bulbCompId(comps) { return comps.find(c => c.type === 'bulb')?.id; }

        // --- 初始化配置 ---
        window.onload = function() {
            generateNewPuzzle(false); // 首次載入不需要強制限制類型
            renderAll();
            updateSolvedRecordsUI();  // 初始化答對挑戰面板
            
            document.addEventListener('keydown', handleKeyDown);
            document.addEventListener('mouseup', handleGlobalMouseUp);
            document.addEventListener('mousemove', handleGlobalMouseMove);

            document.getElementById('canvas-container').addEventListener('contextmenu', e => {
                e.preventDefault();
            });

            // 點擊空白處取消選取
            document.getElementById('canvas-wrapper').addEventListener('mousedown', (e) => {
                if (e.target === e.currentTarget) {
                    selectedElement = null;
                    renderAll();
                }
            });
        };

        // --- 彈出與通知系統 ---
        function showToast(msg, iconClass = 'fa-circle-exclamation', duration = 3000) {
            const toast = document.getElementById('toast');
            const toastMsg = document.getElementById('toast-msg');
            const toastIcon = document.getElementById('toast-icon');
            
            toastMsg.textContent = msg;
            toastIcon.className = `fa-solid ${iconClass}`;
            if (iconClass.includes('check') || iconClass.includes('trophy')) {
                toastIcon.className += ' text-emerald-400';
            } else {
                toastIcon.className += ' text-amber-400';
            }
            
            toast.style.transform = 'translateY(0)';
            toast.style.opacity = '1';
            
            setTimeout(() => {
                toast.style.transform = 'translateY(80px)';
                toast.style.opacity = '0';
            }, duration);
        }

        // --- 謎題生成與隨機系統 ---
        // 優化：新增 respectCurrentTab 參數，當為 true 時只在當前類型（串聯或並聯）中隨機切換謎題
        function generateNewPuzzle(respectCurrentTab = false) {
            let filteredDb = puzzleDatabase;
            
            if (respectCurrentTab) {
                const targetType = currentTab === 'series' ? '串聯電路' : '並聯電路';
                filteredDb = puzzleDatabase.filter(p => p.type === targetType);
            }
            
            // ❌ 核心篩選：排除已經答對過的謎題ID
            let unsolvedDb = filteredDb.filter(p => !solvedPuzzleIds.includes(p.id));
            
            // 💡 容錯機制：如果當前類型的所有謎題都已經被答對解開
            if (unsolvedDb.length === 0) {
                const tabName = currentTab === 'series' ? '串聯' : '並聯';
                if (respectCurrentTab) {
                    showToast(`太強了！已答對所有【${tabName}】挑戰！重新啟用此題庫！`, "fa-trophy", 4000);
                    // 局部重置此類型的答對記錄，以便可以循環出題
                    const filterIds = filteredDb.map(p => p.id);
                    solvedPuzzleIds = solvedPuzzleIds.filter(id => !filterIds.includes(id));
                    updateSolvedRecordsUI();
                    unsolvedDb = filteredDb;
                } else {
                    showToast("太厲害了！你已通關全部挑戰！已為你自動重置題庫！", "fa-trophy", 4000);
                    solvedPuzzleIds = [];
                    updateSolvedRecordsUI();
                    unsolvedDb = puzzleDatabase;
                }
            }

            const idx = Math.floor(Math.random() * unsolvedDb.length);
            currentPuzzle = { ...unsolvedDb[idx] }; 
            
            if (currentPuzzle.id === 1) {
                const targets = [2, 3, 4];
                const selectedTarget = targets[Math.floor(Math.random() * targets.length)];
                currentPuzzle.target = `讓燈泡獲得 ${selectedTarget}V 的電壓`;
                currentPuzzle.desc = `目前總電壓為 6V（請用電池組），有一個定值電阻(5Ω)與一個燈泡(10Ω)，如何連接才能讓燈泡獲得 ${selectedTarget}V 的電壓？（提示：串聯分壓）`;
                currentPuzzle.verify = (components, wires, calcResults) => {
                    const hasBattery = components.some(c => c.type === 'battery_pack');
                    const hasRes = components.some(c => c.type === 'resistor');
                    const hasBulb = components.some(c => c.type === 'bulb');
                    if (!hasBattery || !hasRes || !hasBulb) return { ok: false, msg: "需要使用電池組、定值電阻與燈泡！" };
                    if (!isCircuitClosed(components, wires)) return { ok: false, msg: "電路必須完全閉合通電！" };
                    if (!isPerfectSeries(components, wires)) return { ok: false, msg: "請確保這是一個串聯電路！" };

                    const bulbComp = components.find(c => c.type === 'bulb');
                    const bulbVolts = calcResults[bulbComp.id]?.volts || 0;
                    if (Math.abs(bulbVolts - selectedTarget) < 0.2) {
                        return { ok: true };
                    }
                    return { ok: false, msg: `燈泡目前分得 ${bulbVolts.toFixed(1)}V，與目標 ${selectedTarget}V 不符！` };
                };
            }

            document.getElementById('puzzle-desc').innerHTML = `<i class="fa-solid fa-lightbulb text-amber-500 mr-1 animate-pulse"></i> ${currentPuzzle.desc}`;
            document.getElementById('puzzle-type').textContent = currentPuzzle.type;
            document.getElementById('puzzle-target').textContent = currentPuzzle.target;
            
            // 只有在非限制模式或主動切換時才更變 Tab 標籤頁
            if (!respectCurrentTab) {
                if (currentPuzzle.type === '並聯電路') {
                    switchTab('parallel');
                } else {
                    switchTab('series');
                }
            }
        }

        // --- 畫布標籤頁切換 ---
        function switchTab(tab) {
            currentTab = tab;
            
            const btnSeries = document.getElementById('tab-series');
            const btnParallel = document.getElementById('tab-parallel');
            
            if (tab === 'series') {
                btnSeries.className = "px-5 py-2 rounded-lg font-bold text-sm transition-all shadow-sm bg-blue-600 text-white";
                btnParallel.className = "px-5 py-2 rounded-lg font-bold text-sm transition-all hover:bg-slate-100 text-slate-600";
            } else {
                btnParallel.className = "px-5 py-2 rounded-lg font-bold text-sm transition-all shadow-sm bg-blue-600 text-white";
                btnSeries.className = "px-5 py-2 rounded-lg font-bold text-sm transition-all hover:bg-slate-100 text-slate-600";
            }
            
            if (isSimulating) {
                toggleSimulation();
            }
            
            selectedElement = null;
            renderAll();
        }

        // --- 元件生成 ---
        function spawnComponent(type) {
            const container = document.getElementById('canvas-container');
            const rect = container.getBoundingClientRect();
            
            const x = Math.round(rect.width / 2 - 40 + (Math.random() - 0.5) * 40);
            const y = Math.round(rect.height / 2 - 20 + (Math.random() - 0.5) * 40);
            
            const newComp = {
                id: 'comp_' + Date.now() + '_' + Math.floor(Math.random() * 1000),
                type: type,
                x: x,
                y: y,
                rotation: 0,
                value: compTypes[type].value,
                state: {
                    isOpen: type === 'switch' ? true : false
                }
            };
            
            circuitState[currentTab].components.push(newComp);
            selectedElement = { type: 'component', id: newComp.id };
            
            renderAll();
            showToast(`已新增 ${compTypes[type].name}`, 'fa-plus-circle');
            
            if (isSimulating) {
                runSimulationAnalysis();
            }
        }

        // --- 鍵盤 & 滑鼠事件 ---
        function handleKeyDown(e) {
            if (!selectedElement) return;

            // 支援鍵盤快速鍵刪除
            if (e.key === 'Delete' || e.key === 'Backspace') {
                e.preventDefault();
                if (selectedElement.type === 'component') {
                    deleteSelectedComponentOnly();
                } else if (selectedElement.type === 'wire') {
                    deleteSelectedWireOnly();
                }
            }
            else if (e.key === 'r' || e.key === 'R') {
                e.preventDefault();
                rotateSelected();
            }
        }

        // --- 🛠️ 拆分後的新增刪除函數 ---

        /**
         * 獨立刪除元件函數
         */
        function deleteSelectedComponentOnly() {
            if (!selectedElement || selectedElement.type !== 'component') {
                showToast("請先點擊選中一個要刪除的元件！", "fa-triangle-exclamation");
                return;
            }

            const state = circuitState[currentTab];
            state.components = state.components.filter(c => c.id !== selectedElement.id);
            // 同步刪除所有與此元件相連的導線
            state.wires = state.wires.filter(w => w.fromCompId !== selectedElement.id && w.toCompId !== selectedElement.id);
            
            showToast("已成功移除選中元件及其導線", "fa-trash-can");
            selectedElement = null;
            renderAll();
            
            if (isSimulating) {
                runSimulationAnalysis();
            }
        }

        /**
         * 獨立刪除導線線路函數
         */
        function deleteSelectedWireOnly() {
            if (!selectedElement || selectedElement.type !== 'wire') {
                showToast("請先點擊選中一條要刪除的導線！", "fa-triangle-exclamation");
                return;
            }

            const state = circuitState[currentTab];
            state.wires = state.wires.filter(w => w.id !== selectedElement.id);
            
            showToast("已成功剪斷選中導線", "fa-scissors");
            selectedElement = null;
            renderAll();
            
            if (isSimulating) {
                runSimulationAnalysis();
            }
        }

        function rotateSelected() {
            if (!selectedElement || selectedElement.type !== 'component') return;
            
            const state = circuitState[currentTab];
            const comp = state.components.find(c => c.id === selectedElement.id);
            if (comp) {
                comp.rotation = (comp.rotation + 90) % 360;
                renderAll();
                if (isSimulating) {
                    runSimulationAnalysis();
                }
            }
        }

        function handleGlobalMouseMove(e) {
            if (dragTarget) {
                const container = document.getElementById('canvas-container');
                const rect = container.getBoundingClientRect();
                
                let newX = e.clientX - rect.left - dragOffset.x;
                let newY = e.clientY - rect.top - dragOffset.y;
                
                newX = Math.max(10, Math.min(rect.width - 90, newX));
                newY = Math.max(10, Math.min(rect.height - 50, newY));
                
                newX = Math.round(newX / 10) * 10;
                newY = Math.round(newY / 10) * 10;
                
                dragTarget.x = newX;
                dragTarget.y = newY;
                
                renderAll();
            }
            else if (activeWireStart) {
                const container = document.getElementById('canvas-container');
                const rect = container.getBoundingClientRect();
                const currentX = e.clientX - rect.left;
                const currentY = e.clientY - rect.top;
                
                const tempWire = document.getElementById('temp-wire');
                tempWire.classList.remove('hidden');
                
                const p1x = activeWireStart.termX;
                const p1y = activeWireStart.termY;
                const p2x = currentX;
                const p2y = currentY;
                
                const midX = (p1x + p2x) / 2;
                const pathStr = `M ${p1x} ${p1y} L ${midX} ${p1y} L ${midX} ${p2y} L ${p2x} ${p2y}`;
                tempWire.setAttribute('d', pathStr);
            }
        }

        function handleGlobalMouseUp(e) {
            if (dragTarget) {
                dragTarget = null;
            }
            if (activeWireStart) {
                document.getElementById('temp-wire').classList.add('hidden');
                activeWireStart = null;
            }
        }

        function clearCanvas() {
            circuitState[currentTab].components = [];
            circuitState[currentTab].wires = [];
            selectedElement = null;
            
            if (isSimulating) {
                toggleSimulation();
            }
            renderAll();
            showToast("畫布已清空", "fa-rotate");
        }

        // --- 渲染引擎 ---
        function renderAll() {
            const container = document.getElementById('canvas-container');
            const wiresGroup = document.getElementById('wires-group');
            const guide = document.getElementById('empty-canvas-guide');
            
            container.innerHTML = '';
            wiresGroup.innerHTML = '';
            
            const state = circuitState[currentTab];
            
            if (state.components.length === 0) {
                guide.classList.remove('hidden');
            } else {
                guide.classList.add('hidden');
            }

            // 1. 渲染導線
            state.wires.forEach(wire => {
                const fromComp = state.components.find(c => c.id === wire.fromCompId);
                const toComp = state.components.find(c => c.id === wire.toCompId);
                
                if (fromComp && toComp) {
                    const p1 = getTerminalCoords(fromComp, wire.fromTerminal);
                    const p2 = getTerminalCoords(toComp, wire.toTerminal);
                    
                    const midX = (p1.x + p2.x) / 2;
                    const pathStr = `M ${p1.x} ${p1.y} L ${midX} ${p1.y} L ${midX} ${p2.y} L ${p2.x} ${p2.y}`;
                    
                    // 1a. 渲染視覺導線 (不啟用 pointer-events 以免阻擋 Hitbox 點擊)
                    const path = document.createElementNS("http://www.w3.org/2000/svg", "path");
                    path.setAttribute("d", pathStr);
                    
                    const isSelected = selectedElement && selectedElement.type === 'wire' && selectedElement.id === wire.id;
                    path.setAttribute("stroke", isSelected ? "#ef4444" : (isSimulating && isCircuitClosed(state.components, state.wires) ? "#eab308" : "#475569"));
                    path.setAttribute("stroke-width", isSelected ? "5" : "3");
                    path.setAttribute("fill", "none");
                    path.setAttribute("style", "pointer-events: none;");
                    wiresGroup.appendChild(path);

                    // 1b. 額外渲染一條寬度達 16px 的「透明感應熱區 (Hitbox)」，使滑鼠及觸控極易點中
                    const hitPath = document.createElementNS("http://www.w3.org/2000/svg", "path");
                    hitPath.setAttribute("d", pathStr);
                    hitPath.setAttribute("stroke", "transparent");
                    hitPath.setAttribute("stroke-width", "16");
                    hitPath.setAttribute("fill", "none");
                    hitPath.setAttribute("style", "cursor: pointer; pointer-events: stroke;");
                    
                    hitPath.addEventListener('mousedown', (e) => {
                        e.stopPropagation();
                        selectedElement = { type: 'wire', id: wire.id };
                        renderAll();
                    });
                    wiresGroup.appendChild(hitPath);
                }
            });

            // 2. 渲染元件
            state.components.forEach(comp => {
                const compEl = document.createElement('div');
                // 這裡明確加上 pointer-events-auto 以維持元件本體的點擊功能
                compEl.className = `absolute bg-white/90 p-1 border-2 rounded-xl flex items-center justify-center transition-shadow cursor-move select-none z-20 pointer-events-auto`;
                compEl.style.left = `${comp.x}px`;
                compEl.style.top = `${comp.y}px`;
                compEl.style.width = `80px`;
                compEl.style.height = `40px`;
                compEl.style.transform = `rotate(${comp.rotation}deg)`;
                
                const isSelected = selectedElement && selectedElement.type === 'component' && selectedElement.id === comp.id;
                if (isSelected) {
                    compEl.classList.add('border-red-500', 'shadow-red-200', 'shadow-lg');
                } else {
                    compEl.classList.add('border-slate-300', 'hover:border-indigo-400', 'shadow-sm');
                }

                compEl.innerHTML = getComponentSVG(comp);
                
                compEl.addEventListener('mousedown', (e) => {
                    e.stopPropagation();
                    selectedElement = { type: 'component', id: comp.id };
                    dragTarget = comp;
                    
                    const rect = compEl.getBoundingClientRect();
                    dragOffset = {
                        x: e.clientX - rect.left,
                        y: e.clientY - rect.top
                    };
                    
                    renderAll();
                });

                compEl.addEventListener('contextmenu', (e) => {
                    e.preventDefault();
                    e.stopPropagation();
                    comp.rotation = (comp.rotation + 90) % 360;
                    renderAll();
                    if (isSimulating) runSimulationAnalysis();
                });

                compEl.addEventListener('dblclick', (e) => {
                    e.stopPropagation();
                    if (comp.type === 'switch') {
                        comp.state.isOpen = !comp.state.isOpen;
                        showToast(comp.state.isOpen ? "開關已斷開" : "開關已閉合", comp.state.isOpen ? "fa-toggle-off" : "fa-toggle-on");
                        renderAll();
                        if (isSimulating) runSimulationAnalysis();
                    }
                });

                // 紅色接線端點 (同樣需要 pointer-events-auto)
                const termLeft = document.createElement('div');
                termLeft.className = `absolute w-3.5 h-3.5 bg-red-500 rounded-full border border-white cursor-crosshair z-30 conn-point transition-transform duration-100 pointer-events-auto`;
                termLeft.style.left = `-7px`;
                termLeft.style.top = `13px`;
                
                const termRight = document.createElement('div');
                termRight.className = `absolute w-3.5 h-3.5 bg-red-500 rounded-full border border-white cursor-crosshair z-30 conn-point transition-transform duration-100 pointer-events-auto`;
                termRight.style.right = `-7px`;
                termRight.style.top = `13px`;

                termLeft.addEventListener('mousedown', (e) => startWiring(e, comp.id, 0));
                termRight.addEventListener('mousedown', (e) => startWiring(e, comp.id, 1));
                
                termLeft.addEventListener('mouseup', (e) => endWiring(e, comp.id, 0));
                termRight.addEventListener('mouseup', (e) => endWiring(e, comp.id, 1));

                compEl.appendChild(termLeft);
                compEl.appendChild(termRight);
                
                container.appendChild(compEl);

                if (comp.type === 'rheostat' && isSelected) {
                    renderRheostatSlider(comp);
                }
            });

            // 3. 新增動態控制刪除按鈕狀態
            const deleteCompBtn = document.getElementById('delete-comp-btn');
            const deleteWireBtn = document.getElementById('delete-wire-btn');

            if (selectedElement && selectedElement.type === 'component') {
                deleteCompBtn.disabled = false;
                deleteCompBtn.className = "px-3 py-2 bg-rose-500 hover:bg-rose-600 active:bg-rose-700 text-white rounded-lg text-sm font-bold transition flex items-center gap-1 shadow-md";
            } else {
                deleteCompBtn.disabled = true;
                deleteCompBtn.className = "px-3 py-2 bg-slate-100 text-slate-400 cursor-not-allowed rounded-lg text-sm font-bold transition flex items-center gap-1";
            }

            if (selectedElement && selectedElement.type === 'wire') {
                deleteWireBtn.disabled = false;
                deleteWireBtn.className = "px-3 py-2 bg-orange-500 hover:bg-orange-600 active:bg-orange-700 text-white rounded-lg text-sm font-bold transition flex items-center gap-1 shadow-md";
            } else {
                deleteWireBtn.disabled = true;
                deleteWireBtn.className = "px-3 py-2 bg-slate-100 text-slate-400 cursor-not-allowed rounded-lg text-sm font-bold transition flex items-center gap-1";
            }
        }

        function getTerminalCoords(comp, terminalIdx) {
            const w = 80;
            const h = 40;
            const cx = comp.x + w / 2;
            const cy = comp.y + h / 2;
            let ox = terminalIdx === 0 ? -w / 2 : w / 2;
            let oy = 0;
            const rad = (comp.rotation * Math.PI) / 180;
            const rx = ox * Math.cos(rad) - oy * Math.sin(rad);
            const ry = ox * Math.sin(rad) + oy * Math.cos(rad);
            return {
                x: Math.round(cx + rx),
                y: Math.round(cy + ry)
            };
        }

        function renderRheostatSlider(comp) {
            const container = document.getElementById('canvas-container');
            const sliderDiv = document.createElement('div');
            sliderDiv.className = "absolute bg-slate-800 text-white p-2 rounded-lg text-xs shadow-lg flex flex-col gap-1 pointer-events-auto z-40";
            sliderDiv.style.left = `${comp.x - 20}px`;
            sliderDiv.style.top = `${comp.y - 65}px`;
            sliderDiv.style.width = "120px";
            
            sliderDiv.innerHTML = `
                <div class="flex justify-between font-bold text-[10px]">
                    <span>變阻器: ${comp.value.toFixed(1)}Ω</span>
                </div>
                <input type="range" min="1" max="20" step="0.5" value="${comp.value}" 
                    class="w-full h-1 bg-slate-600 rounded-lg appearance-none cursor-pointer"
                    id="slider-${comp.id}">
            `;
            
            container.appendChild(sliderDiv);
            
            sliderDiv.addEventListener('mousedown', e => e.stopPropagation());
            
            const input = document.getElementById(`slider-${comp.id}`);
            input.addEventListener('input', (e) => {
                comp.value = parseFloat(e.target.value);
                comp.res = comp.value;
                sliderDiv.querySelector('span').textContent = `變阻器: ${comp.value.toFixed(1)}Ω`;
                if (isSimulating) {
                    runSimulationAnalysis();
                }
            });
        }

        function startWiring(e, compId, terminalIdx) {
            e.stopPropagation();
            e.preventDefault();
            const state = circuitState[currentTab];
            const comp = state.components.find(c => c.id === compId);
            const coords = getTerminalCoords(comp, terminalIdx);
            activeWireStart = {
                compId: compId,
                terminal: terminalIdx,
                termX: coords.x,
                termY: coords.y
            };
        }

        function endWiring(e, compId, terminalIdx) {
            e.stopPropagation();
            if (!activeWireStart) return;
            if (activeWireStart.compId === compId) {
                activeWireStart = null;
                document.getElementById('temp-wire').classList.add('hidden');
                return;
            }
            
            const state = circuitState[currentTab];
            const newWire = {
                id: 'wire_' + Date.now() + '_' + Math.floor(Math.random() * 100),
                fromCompId: activeWireStart.compId,
                fromTerminal: activeWireStart.terminal,
                toCompId: compId,
                toTerminal: terminalIdx
            };
            
            const exists = state.wires.some(w => 
                (w.fromCompId === newWire.fromCompId && w.fromTerminal === newWire.fromTerminal && w.toCompId === newWire.toCompId && w.toTerminal === newWire.toTerminal) ||
                (w.fromCompId === newWire.toCompId && w.fromTerminal === newWire.toTerminal && w.toCompId === newWire.fromCompId && w.toTerminal === newWire.fromTerminal)
            );
            
            if (!exists) {
                state.wires.push(newWire);
                showToast("連線成功！", "fa-link");
            }
            
            activeWireStart = null;
            document.getElementById('temp-wire').classList.add('hidden');
            renderAll();
            
            if (isSimulating) {
                runSimulationAnalysis();
            }
        }

        function getComponentSVG(comp) {
            let svgContent = '';
            const color = 'currentColor';
            const isOn = isSimulating && isCircuitClosed(circuitState[currentTab].components, circuitState[currentTab].wires);
            const textClass = "fill-slate-600 text-[10px] font-bold";

            switch (comp.type) {
                case 'battery':
                    svgContent = `
                        <line x1="0" y1="20" x2="35" y2="20" stroke="${color}" stroke-width="2"/>
                        <line x1="35" y1="10" x2="35" y2="30" stroke="${color}" stroke-width="4"/>
                        <line x1="45" y1="15" x2="45" y2="25" stroke="${color}" stroke-width="2"/>
                        <line x1="45" y1="20" x2="80" y2="20" stroke="${color}" stroke-width="2"/>
                        <text x="22" y="14" class="${textClass}">+</text>
                        <text x="50" y="14" class="${textClass}">1.5V</text>
                    `;
                    break;
                case 'battery_pack':
                    svgContent = `
                        <line x1="0" y1="20" x2="25" y2="20" stroke="${color}" stroke-width="2"/>
                        <line x1="25" y1="10" x2="25" y2="30" stroke="${color}" stroke-width="4"/>
                        <line x1="32" y1="15" x2="32" y2="25" stroke="${color}" stroke-width="2"/>
                        <line x1="39" y1="10" x2="39" y2="30" stroke="${color}" stroke-width="4"/>
                        <line x1="46" y1="15" x2="46" y2="25" stroke="${color}" stroke-width="2"/>
                        <line x1="53" y1="10" x2="53" y2="30" stroke="${color}" stroke-width="4"/>
                        <line x1="60" y1="15" x2="60" y2="25" stroke="${color}" stroke-width="2"/>
                        <line x1="60" y1="20" x2="80" y2="20" stroke="${color}" stroke-width="2"/>
                        <text x="14" y="14" class="${textClass}">+</text>
                        <text x="63" y="14" class="${textClass}">6V</text>
                    `;
                    break;
                case 'bulb':
                    const glowColor = isOn ? "#fef08a" : "none";
                    const strokeColor = isOn ? "#eab308" : color;
                    svgContent = `
                        <line x1="0" y1="20" x2="30" y2="20" stroke="${strokeColor}" stroke-width="2"/>
                        <circle cx="40" cy="20" r="11" fill="${glowColor}" stroke="${strokeColor}" stroke-width="2" class="${isOn ? 'animate-pulse' : ''}"/>
                        <line x1="32" y1="12" x2="48" y2="28" stroke="${strokeColor}" stroke-width="2"/>
                        <line x1="48" y1="12" x2="32" y2="28" stroke="${strokeColor}" stroke-width="2"/>
                        <line x1="50" y1="20" x2="80" y2="20" stroke="${strokeColor}" stroke-width="2"/>
                    `;
                    break;
                case 'switch':
                    const isOpen = comp.state.isOpen;
                    svgContent = isOpen ? `
                        <line x1="0" y1="20" x2="30" y2="20" stroke="${color}" stroke-width="2"/>
                        <circle cx="30" cy="20" r="2.5" fill="currentColor"/>
                        <line x1="30" y1="20" x2="50" y2="8" stroke="${color}" stroke-width="2.5"/>
                        <circle cx="50" cy="20" r="2.5" fill="currentColor"/>
                        <line x1="50" y1="20" x2="80" y2="20" stroke="${color}" stroke-width="2"/>
                        <text x="25" y="36" class="${textClass}">開 (斷)</text>
                    ` : `
                        <line x1="0" y1="20" x2="30" y2="20" stroke="${color}" stroke-width="2"/>
                        <circle cx="30" cy="20" r="2.5" fill="currentColor"/>
                        <line x1="30" y1="20" x2="50" y2="20" stroke="${color}" stroke-width="2.5"/>
                        <circle cx="50" cy="20" r="2.5" fill="currentColor"/>
                        <line x1="50" y1="20" x2="80" y2="20" stroke="${color}" stroke-width="2"/>
                        <text x="25" y="36" class="${textClass}">關 (通)</text>
                    `;
                    break;
                case 'resistor':
                    svgContent = `
                        <line x1="0" y1="20" x2="25" y2="20" stroke="${color}" stroke-width="2"/>
                        <rect x="25" y="14" width="30" height="12" fill="none" stroke="${color}" stroke-width="2"/>
                        <line x1="55" y1="20" x2="80" y2="20" stroke="${color}" stroke-width="2"/>
                        <text x="32" y="34" class="${textClass}">5 Ω</text>
                    `;
                    break;
                case 'rheostat':
                    svgContent = `
                        <line x1="0" y1="20" x2="25" y2="20" stroke="${color}" stroke-width="2"/>
                        <rect x="25" y="14" width="30" height="12" fill="none" stroke="${color}" stroke-width="2"/>
                        <line x1="55" y1="20" x2="80" y2="20" stroke="${color}" stroke-width="2"/>
                        <line x1="18" y1="32" x2="58" y2="6" stroke="${color}" stroke-width="1.5"/>
                        <polygon points="58,6 54,10 58,10" fill="currentColor" stroke="${color}" stroke-width="1"/>
                        <text x="32" y="34" class="${textClass}">${comp.value.toFixed(1)}Ω</text>
                    `;
                    break;
                case 'ammeter':
                    let displayCurrent = "A";
                    if (isOn && globalSimResults && globalSimResults[comp.id]) {
                        displayCurrent = globalSimResults[comp.id].current.toFixed(2) + "A";
                    }
                    svgContent = `
                        <line x1="0" y1="20" x2="25" y2="20" stroke="${color}" stroke-width="2"/>
                        <circle cx="40" cy="20" r="14" fill="white" stroke="${color}" stroke-width="2"/>
                        <text x="40" y="24" font-size="12" font-weight="extrabold" text-anchor="middle" fill="currentColor">${displayCurrent}</text>
                        <line x1="55" y1="20" x2="80" y2="20" stroke="${color}" stroke-width="2"/>
                    `;
                    break;
                case 'voltmeter':
                    let displayVolts = "V";
                    if (isOn && globalSimResults && globalSimResults[comp.id]) {
                        displayVolts = globalSimResults[comp.id].volts.toFixed(1) + "V";
                    }
                    svgContent = `
                        <line x1="0" y1="20" x2="25" y2="20" stroke="${color}" stroke-width="2"/>
                        <circle cx="40" cy="20" r="14" fill="white" stroke="${color}" stroke-width="2"/>
                        <text x="40" y="24" font-size="12" font-weight="extrabold" text-anchor="middle" fill="currentColor">${displayVolts}</text>
                        <line x1="55" y1="20" x2="80" y2="20" stroke="${color}" stroke-width="2"/>
                    `;
                    break;
            }

            return `<svg class="w-full h-full text-slate-800" viewBox="0 0 80 40">${svgContent}</svg>`;
        }

        // ==================================================
        // ======= ⚡ DFS 支路尋找與高保真電路模擬引擎 ======
        // ==================================================

        /**
         * 使用 DFS 尋找從電池正極出發，回到電池負極的所有完整閉合支路 (Paths)
         */
        function findBranches(components, wires) {
            const battery = components.find(c => c.type === 'battery' || c.type === 'battery_pack');
            if (!battery) return [];

            const paths = [];
            const visitedComps = new Set();

            function dfs(currCompId, currTerm, currentPath) {
                // 如果回到了電池，且是從端點0 (負極) 進入，則成功完成了一條閉合支路！
                if (currCompId === battery.id) {
                    if (currTerm === 0) {
                        paths.push(currentPath);
                    }
                    return;
                }

                visitedComps.add(currCompId);
                const otherTerm = 1 - currTerm; // 該元件的對向端點
                const updatedPath = [...currentPath, { compId: currCompId, termIn: currTerm, termOut: otherTerm }];

                // 尋找所有連接到 otherTerm 的導線
                const matchingWires = wires.filter(w => 
                    (w.fromCompId === currCompId && w.fromTerminal === otherTerm) ||
                    (w.toCompId === currCompId && w.toTerminal === otherTerm)
                );

                for (const wire of matchingWires) {
                    const nextCompId = wire.fromCompId === currCompId ? wire.toCompId : wire.fromCompId;
                    const nextTerm = wire.fromCompId === currCompId ? wire.toTerminal : wire.fromTerminal;

                    // 避免在單一分支內重複訪問相同元件（除非是回到電池負極）
                    if (!visitedComps.has(nextCompId) || (nextCompId === battery.id && nextTerm === 0)) {
                        dfs(nextCompId, nextTerm, updatedPath);
                    }
                }

                visitedComps.delete(currCompId);
            }

            // 從電池正極 (端點1) 出發
            const startWires = wires.filter(w => 
                (w.fromCompId === battery.id && w.fromTerminal === 1) ||
                (w.toCompId === battery.id && w.toTerminal === 1)
            );

            for (const wire of startWires) {
                const nextCompId = wire.fromCompId === battery.id ? wire.toCompId : wire.fromCompId;
                const nextTerm = wire.fromCompId === battery.id ? wire.toTerminal : wire.fromTerminal;
                dfs(nextCompId, nextTerm, []);
            }

            return paths;
        }

        /**
         * 判定當前電路是否完全接通閉合
         */
        function isCircuitClosed(components, wires) {
            return findBranches(components, wires).length > 0;
        }

        /**
         * 判斷是否為完美的純串聯電路 (只有一條主迴路，無任何分流支路)
         */
        function isPerfectSeries(components, wires) {
            const branches = findBranches(components, wires);
            if (branches.length !== 1) return false; // 必須有且僅有一條路徑
            
            // 該路徑應該包含畫布上所有主要的非電錶元件 (排除電錶)
            const mainCompsOnCanvas = components.filter(c => c.type !== 'voltmeter' && c.type !== 'ammeter' && c.type !== 'battery' && c.type !== 'battery_pack');
            const path = branches[0];
            
            return mainCompsOnCanvas.every(c => path.some(node => node.compId === c.id));
        }

        /**
         * 💡 等電位端點連接分析演算法 (BFS)
         * 判斷元件 A 的特定端點與元件 B 的特定端點是否通過純導線（無負載）連接在同一個電位節點上
         */
        function areTerminalsConnected(c1, t1, c2, t2, wires) {
            const visited = new Set();
            const queue = [[c1, t1]];
            visited.add(`${c1}_${t1}`);
            
            while (queue.length > 0) {
                const [currComp, currTerm] = queue.shift();
                if (currComp === c2 && currTerm === t2) return true;
                
                // 尋找與當前端點直接相連的其他元件端點
                wires.forEach(w => {
                    let nextComp = null;
                    let nextTerm = null;
                    
                    if (w.fromCompId === currComp && w.fromTerminal === currTerm) {
                        nextComp = w.toCompId;
                        nextTerm = w.toTerminal;
                    } else if (w.toCompId === currComp && w.toTerminal === currTerm) {
                        nextComp = w.fromCompId;
                        nextTerm = w.fromTerminal;
                    }
                    
                    if (nextComp !== null) {
                        const key = `${nextComp}_${nextTerm}`;
                        if (!visited.has(key)) {
                            visited.add(key);
                            queue.push([nextComp, nextTerm]);
                        }
                    }
                });
            }
            return false;
        }

        /**
         * 判定兩個元件是否為完美的並聯連接 (兩端皆各自連通)
         */
        function checkParallel(idA, idB, wires) {
            const connected0_0 = areTerminalsConnected(idA, 0, idB, 0, wires);
            const connected0_1 = areTerminalsConnected(idA, 0, idB, 1, wires);
            const connected1_0 = areTerminalsConnected(idA, 1, idB, 0, wires);
            const connected1_1 = areTerminalsConnected(idA, 1, idB, 1, wires);
            
            // 端點 0 連 0 且 1 連 1，或者端點 0 連 1 且 1 連 0 (元件方向相反)
            return (connected0_0 && connected1_1) || (connected0_1 && connected1_0);
        }

        // --- 全域模擬計算入口 ---
        let globalSimResults = {};

        function toggleSimulation() {
            const state = circuitState[currentTab];
            if (state.components.length === 0) {
                showToast("畫布上沒有元件可以通電！", "fa-triangle-exclamation");
                return;
            }

            isSimulating = !isSimulating;
            const btn = document.getElementById('simulate-btn');
            const statusText = document.getElementById('status-text');
            const dash = document.getElementById('sim-dashboard');
            
            if (isSimulating) {
                btn.innerHTML = `<i class="fa-solid fa-power-off text-emerald-400"></i> 開啟通電`;
                btn.classList.replace('bg-amber-500', 'bg-emerald-700');
                btn.classList.replace('hover:bg-amber-600', 'hover:bg-emerald-800');
                runSimulationAnalysis();
            } else {
                btn.innerHTML = `<i class="fa-solid fa-power-off"></i> 通電閉合`;
                btn.classList.replace('bg-emerald-700', 'bg-amber-500');
                btn.classList.replace('hover:bg-emerald-800', 'hover:bg-amber-600');
                statusText.textContent = "斷路";
                statusText.className = "font-semibold text-emerald-400";
                
                dash.classList.add('hidden');
                globalSimResults = {};
                renderAll();
            }
        }

        function runSimulationAnalysis() {
            const state = circuitState[currentTab];
            const statusText = document.getElementById('status-text');
            const dash = document.getElementById('sim-dashboard');
            
            if (!isSimulating) return;

            const battery = state.components.find(c => c.type === 'battery' || c.type === 'battery_pack');
            if (!battery) {
                statusText.textContent = "斷路 (無電源)";
                statusText.className = "font-semibold text-amber-500";
                dash.classList.add('hidden');
                globalSimResults = {};
                renderAll();
                return;
            }

            const branches = findBranches(state.components, state.wires);
            
            // 排除含有「開啟狀態的開關」的無效支路 (伏特計等極高阻支路不影響主迴路)
            const activeBranches = branches.filter(path => {
                return !path.some(node => {
                    const comp = state.components.find(c => c.id === node.compId);
                    return comp && comp.type === 'switch' && comp.state.isOpen;
                });
            });

            if (activeBranches.length === 0) {
                statusText.textContent = "斷路 (無電流)";
                statusText.className = "font-semibold text-amber-500";
                dash.classList.add('hidden');
                globalSimResults = {};
                renderAll();
                return;
            }

            statusText.textContent = "通路 (正常通電中)";
            statusText.className = "font-semibold text-emerald-400";
            dash.classList.remove('hidden');

            let totalEMF = 0;
            state.components.forEach(c => {
                if (c.type === 'battery') totalEMF += 1.5;
                if (c.type === 'battery_pack') totalEMF += 6.0;
            });

            // 初始化所有元件數值
            const results = {};
            state.components.forEach(c => {
                results[c.id] = { current: 0, volts: 0 };
            });

            let totalCurrent = 0;

            // 對於每一條有效的閉合支路，根據歐姆定律計算電流分佈
            activeBranches.forEach(path => {
                let branchRes = compTypes[battery.type].res; // 電池內阻
                path.forEach(node => {
                    if (node.compId === battery.id) return;
                    const comp = state.components.find(c => c.id === node.compId);
                    if (comp) {
                        branchRes += (comp.type === 'rheostat') ? comp.value : compTypes[comp.type].res;
                    }
                });

                const branchCurrent = totalEMF / branchRes;
                totalCurrent += branchCurrent;

                path.forEach(node => {
                    if (node.compId === battery.id) return;
                    const comp = state.components.find(c => c.id === node.compId);
                    if (comp) {
                        results[comp.id].current += branchCurrent;
                        const r = (comp.type === 'rheostat') ? comp.value : compTypes[comp.type].res;
                        results[comp.id].volts += branchCurrent * r;
                    }
                });
            });

            // 電池本體的電流與端電壓
            results[battery.id] = {
                current: totalCurrent,
                volts: totalEMF - totalCurrent * compTypes[battery.type].res
            };

            // ================================================
            // 🏆 核心修正：伏特計並聯電壓「映射」後製處理
            // ================================================
            state.components.forEach(v => {
                if (v.type === 'voltmeter') {
                    // 尋找與該伏特計並聯的其它非電錶元件
                    let targetComp = null;
                    state.components.forEach(c => {
                        if (c.id !== v.id && c.type !== 'voltmeter' && c.type !== 'ammeter' && checkParallel(v.id, c.id, state.wires)) {
                            targetComp = c;
                        }
                    });
                    
                    if (targetComp) {
                        // 伏特計極高阻不分流，直接同步映射被測量元件的端電壓
                        results[v.id].volts = results[targetComp.id].volts;
                    } else {
                        // 檢查是否直接並聯接在電池兩端
                        if (checkParallel(v.id, battery.id, state.wires)) {
                            results[v.id].volts = results[battery.id].volts;
                        }
                    }
                }
            });

            globalSimResults = results;

            // 更新儀表板 UI
            document.getElementById('dash-current').textContent = `${totalCurrent.toFixed(2)} A`;
            const eqRes = totalCurrent > 0 ? (totalEMF / totalCurrent) : 0;
            document.getElementById('dash-res').textContent = `${eqRes.toFixed(1)} Ω`;
            
            const rheo = state.components.find(c => c.type === 'rheostat');
            if (rheo) {
                document.getElementById('dash-rheo').textContent = `${rheo.value.toFixed(1)} Ω`;
            } else {
                document.getElementById('dash-rheo').textContent = "無";
            }

            renderAll();
        }

        // --- 答案驗證 ---
        function verifyAnswer() {
            const state = circuitState[currentTab];
            
            if (state.components.length === 0) {
                showToast("畫布上沒有任何元件，請先擺放電路！", "fa-triangle-exclamation");
                return;
            }

            if (!isSimulating) {
                showToast("請點擊【通電閉合】開啟電路模擬，再進行驗證！", "fa-circle-exclamation");
                return;
            }

            const check = currentPuzzle.verify(state.components, state.wires, globalSimResults);
            
            if (check.ok) {
                score += 10;
                document.getElementById('score-display').textContent = `得分: ${score}`;
                
                // 🏆 核心邏輯：將當前謎題ID加入「答對列表」中 (防重複)
                if (!solvedPuzzleIds.includes(currentPuzzle.id)) {
                    solvedPuzzleIds.push(currentPuzzle.id);
                    updateSolvedRecordsUI();
                }

                if (navigator.vibrate) navigator.vibrate(200);
                
                document.getElementById('success-message').innerHTML = `
                    <strong>${currentPuzzle.title} 成功解答！</strong><br>
                    您完美達成了：<span class="text-emerald-600 font-bold">${currentPuzzle.target}</span> 的目標。<br>
                    此題目已被自動移出抽選名單，並點亮底部的成就徽章！
                `;
                document.getElementById('success-modal').classList.remove('hidden');
            } else {
                showToast(check.msg || "電路不合適，請調整元件與接線！", "fa-circle-xmark");
            }
        }

        // --- 說明面板控制 ---
        function showHelpModal() {
            document.getElementById('help-modal').classList.remove('hidden');
        }

        function closeHelpModal() {
            document.getElementById('help-modal').classList.add('hidden');
        }

        // --- 動態渲染底部答對挑戰看板 ---
        function updateSolvedRecordsUI() {
            const container = document.getElementById('solved-records-container');
            
            if (solvedPuzzleIds.length === 0) {
                container.innerHTML = `<p class="text-xs text-slate-400 italic">目前尚未有答對的記錄。動動手，開啟通電並點擊「驗證答案」來點亮你的第一個成就徽章吧！</p>`;
                return;
            }

            container.innerHTML = solvedPuzzleIds.map(id => {
                const puzzle = puzzleDatabase.find(p => p.id === id);
                if (!puzzle) return '';
                
                const themeClass = puzzle.type === '串聯電路' 
                    ? 'bg-blue-50 text-blue-700 border-blue-200' 
                    : 'bg-purple-50 text-purple-700 border-purple-200';
                
                return `
                    <div class="flex items-center gap-2 px-3 py-2 rounded-xl border font-bold text-xs shadow-sm ${themeClass} animate-fade-in">
                        <i class="fa-solid fa-medal text-amber-500 text-sm"></i>
                        <span>[${puzzle.type}] ${puzzle.title}：${puzzle.target}</span>
                    </div>
                `;
            }).join('');
        }

        // --- 重設所有答對挑戰記錄 ---
        function resetSolvedPuzzles() {
            solvedPuzzleIds = [];
            updateSolvedRecordsUI();
            showToast("已成功重設所有答對挑戰記錄！", "fa-rotate-left");
        }

        function closeSuccessModal() {
            document.getElementById('success-modal').classList.add('hidden');
        }

        function nextPuzzleAndClose() {
            closeSuccessModal();
            if (isSimulating) {
                toggleSimulation();
            }
            clearCanvas();
            generateNewPuzzle(true); // 進入下一關時，同樣保持在原先的畫布類型
        }


        // ==========================================
        // ======= ✨ GEMINI LLM INTEGRATION ========
        // ==========================================

        /**
         * 呼叫 Gemini 2.5 API (具備 5 次指數退避重試邏輯)
         */
        async function callGeminiAPI(prompt, systemInstruction = "") {
            const apiKey = ""; // Canvas 運行環境將自動替換此空字串為有效金鑰
            const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
            
            const payload = {
                contents: [{ parts: [{ text: prompt }] }]
            };
            
            if (systemInstruction) {
                payload.systemInstruction = { parts: [{ text: systemInstruction }] };
            }

            let delay = 1000; // 初始延遲 1秒
            for (let i = 0; i < 5; i++) {
                try {
                    const response = await fetch(url, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });
                    
                    if (response.ok) {
                        const data = await response.json();
                        return data.candidates?.[0]?.content?.parts?.[0]?.text || "抱歉，我現在無法組織回覆內容。";
                    }
                } catch (error) {
                    // 默默重試
                }
                
                // 指數退避等待：1s, 2s, 4s, 8s, 16s
                await new Promise(resolve => setTimeout(resolve, delay));
                delay *= 2;
            }
            throw new Error("系統繁忙中，經多次嘗試仍未能成功連線至 AI 物理老師。請稍候再試！");
        }

        /**
         * 輔助函數：將當前畫布上的元件與狀態包裝成易於 LLM 理解的繁體中文簡報
         */
        function serializeCanvasState() {
            const state = circuitState[currentTab];
            if (state.components.length === 0) {
                return "當前畫布沒有任何元件。";
            }

            let summary = `【當前工作畫布種類】: ${currentTab === 'series' ? '串聯工作區' : '並聯工作區'}\n`;
            summary += `【已放置元件清單】:\n`;
            
            state.components.forEach((c, idx) => {
                const info = compTypes[c.type];
                summary += `${idx + 1}. 元件ID: ${c.id}, 類型: ${info.name}, 阻值/電壓: ${c.value}${info.unit}, 旋轉角度: ${c.rotation}度`;
                if (c.type === 'switch') {
                    summary += `, 狀態: ${c.state.isOpen ? '斷開(不導通)' : '閉合(導通)'}`;
                }
                summary += `\n`;
            });

            summary += `【連線線路資訊】: 共有 ${state.wires.length} 條連線。\n`;
            state.wires.forEach((w, idx) => {
                const fromC = state.components.find(c => c.id === w.fromCompId);
                const toC = state.components.find(c => c.id === w.toCompId);
                if (fromC && toC) {
                    const fromName = compTypes[fromC.type].name;
                    const toName = compTypes[toC.type].name;
                    summary += `   - 線路 ${idx + 1}: ${fromName}(端點 ${w.fromTerminal === 0 ? '左' : '右'}) 連接到 ${toName}(端點 ${w.toTerminal === 0 ? '左' : '右'})\n`;
                }
            });

            summary += `【電路通電物理量】:\n`;
            summary += `- 通電狀態: ${isSimulating ? '已點擊通電閉合' : '目前處於斷電狀態'}\n`;
            
            if (isSimulating && Object.keys(globalSimResults).length > 0) {
                summary += `- 總電流: ${document.getElementById('dash-current').textContent}\n`;
                summary += `- 總等效電阻: ${document.getElementById('dash-res').textContent}\n`;
                summary += `- 各元件分壓及分流情況:\n`;
                state.components.forEach(c => {
                    const res = globalSimResults[c.id];
                    if (res) {
                        summary += `   * [${compTypes[c.type].name}] 電壓: ${res.volts.toFixed(2)}V, 電流: ${res.current.toFixed(2)}A\n`;
                    }
                });
            } else {
                summary += `- 電路目前無電流或未閉合通電。\n`;
            }

            return summary;
        }

        /**
         * ✨ 請求 AI 提示 功能
         */
        async function askAIForHint() {
            const container = document.getElementById('ai-response-container');
            const loader = document.getElementById('ai-loader');
            
            container.classList.add('hidden');
            loader.classList.remove('hidden');

            const canvasDesc = serializeCanvasState();
            
            const systemPrompt = `你是一位熱心、幽默且富有教學經驗的台灣高中/國中物理老師。
你的任務是為正在做「電學模擬實驗」的學生提供引導式、啟發式（不直接給出答案）的提示。
請遵循以下教學原則：
1. 絕不直接透露如何接線的標準答案，而是反問或指出需要改進的物理學觀點。
2. 指引學生注意特定的物理定理，如：
   - 串聯電路中，電阻大小會決定電壓如何分配（串聯分壓定理）。
   - 並聯電路中，每個支路的電壓與電源電動勢相同，但電流與電阻成反比（並聯分流）。
   - 檢查開關是否忘記閉合、導線是否真的有連通電池的兩極、儀表（安培計串聯、伏特計並聯）是否連接錯誤。
3. 請使用溫柔、親切的中文繁體（台灣物理習慣術語，如：『定值電阻』、『開關』、『伏特計』、『安培計』、『串聯』、『並聯』、『變阻器』）。`;

            const userPrompt = `
學生目前正在挑戰的謎題目標：
- 題目標題: ${currentPuzzle.title}
- 題目描述: ${currentPuzzle.desc}
- 目標要求: ${currentPuzzle.target}

學生當前的畫布結構與模擬狀態：
${canvasDesc}

請以物理老師的身份，親切地對學生說幾句提示。字數約在 120-180 字內。請多使用探究式問句！`;

            try {
                const hintText = await callGeminiAPI(userPrompt, systemPrompt);
                container.innerHTML = `<div class="space-y-2 text-indigo-200">
                    <p class="font-bold text-amber-300 flex items-center gap-1"><i class="fa-solid fa-graduation-cap"></i> AI 物理導師引導提示：</p>
                    <p class="italic text-slate-200 leading-relaxed">${hintText.replace(/\n/g, '<br>')}</p>
                </div>`;
            } catch (err) {
                container.innerHTML = `<p class="text-rose-400 font-bold"><i class="fa-solid fa-triangle-exclamation"></i> 出錯了：${err.message}</p>`;
            } finally {
                loader.classList.add('hidden');
                container.classList.remove('hidden');
            }
        }

        /**
         * ✨ AI 電路分析 功能
         */
        async function explainCircuitWithAI() {
            const container = document.getElementById('ai-response-container');
            const loader = document.getElementById('ai-loader');
            
            container.classList.add('hidden');
            loader.classList.remove('hidden');

            const state = circuitState[currentTab];
            if (state.components.length === 0) {
                container.innerHTML = `<p class="text-amber-400 italic text-center py-4">您的工作區目前空空如也，請先從工具箱放些電路元件上來，AI 才能為您 analysis 喔！</p>`;
                loader.classList.add('hidden');
                container.classList.remove('hidden');
                return;
            }

            const canvasDesc = serializeCanvasState();

            const systemPrompt = `走你是一位專業的物理電路學教授、電氣工程師。你的任務是嚴謹且通俗地向中學生分析他們自己設計的電路。
請針對他們在畫布上擺放的所有元件、接線方式，以及此時通電後的各項物理數值（電壓、電流、電阻）進行 analysis。
if (他們的電路是斷開的) { 請說明哪裡可能漏接了、或者開關是否處於開啟狀態。 }
if (發生短路) { 請嚴厲警告並解釋危險性。 }
請全部使用繁體中文（台灣標準物理名詞，如：分壓、分流、滑動變阻器、安培計、歐姆定律）。`;

            const userPrompt = `
請分析以下電路的結構，告訴我這是一個怎樣的電路，它的物理特性是什麼？

${canvasDesc}

請提供條理清晰的分析，字數大約 150-250 字。`;

            try {
                const analysisText = await callGeminiAPI(userPrompt, systemPrompt);
                container.innerHTML = `<div class="space-y-2 text-indigo-200">
                    <p class="font-bold text-cyan-300 flex items-center gap-1"><i class="fa-solid fa-chart-simple"></i> AI 專業電路學分析：</p>
                    <p class="text-slate-200 leading-relaxed text-[11px]">${analysisText.replace(/\n/g, '<br>')}</p>
                </div>`;
            } catch (err) {
                container.innerHTML = `<p class="text-rose-400 font-bold"><i class="fa-solid fa-triangle-exclamation"></i> 出錯了：${err.message}</p>`;
            } finally {
                loader.classList.add('hidden');
                container.classList.remove('hidden');
            }
        }

        /**
         * ✨ AI 自由提問 功能 (取代舊出題功能)
         */
        async function askAIQuestion() {
            const input = document.getElementById('ai-question-input');
            const question = input.value.trim();
            if (!question) {
                showToast("請輸入您想詢問的物理或電路問題！", "fa-triangle-exclamation");
                return;
            }

            const container = document.getElementById('ai-response-container');
            const loader = document.getElementById('ai-loader');
            
            container.classList.add('hidden');
            loader.classList.remove('hidden');
            showToast("正在傳送問題給 AI 老師...", "fa-paper-plane");

            const canvasDesc = serializeCanvasState();

            const systemPrompt = `你是一位熱心、專業且富有 Patience 的高中/國中物理理化老師。
你的任務是解答學生對於電路學、物理現象、基本歐姆定律，或「當前畫布上實驗電路狀態」的自由提問。
請遵循以下教學引導原則：
1. 結合學生當前電路的實際狀態（如果提問與電路有關）來回答。如果學生說「為什麼燈泡不亮？」，請幫他檢查電路狀態並指出問題（例如開關未閉合、缺少電源或根本沒有形成閉合迴路）。
2. 用通俗易懂、生動有趣的方式解釋物理概念，可以多用譬喻（例如水流與水壓比喻電流與電壓），幫助學生建立長期的物理直覺。
3. 請全部使用繁體中文（台灣標準物理名詞，如：分壓、分流、滑動變阻器、安培計、伏特計、短路、斷路、通路、歐姆定律）。`;

            const userPrompt = `
學生提出的物理電學問題：
【${question}】

學生當前的畫布結構與模擬狀態（供你分析參考，幫助學生除錯或舉例說明）：
${canvasDesc}

請以親切、專業的物理老師口吻為學生解惑。若需要，可以引導學生觀察他們在畫布上擺放的元件。字數限制在 150 - 250 字內，層次分明。`;

            try {
                const answerText = await callGeminiAPI(userPrompt, systemPrompt);
                container.innerHTML = `<div class="space-y-2 text-indigo-200">
                    <p class="font-bold text-emerald-400 flex items-center gap-1"><i class="fa-solid fa-comment-dots"></i> AI 老師的解惑：</p>
                    <p class="text-slate-200 leading-relaxed text-[11px]">${answerText.replace(/\n/g, '<br>')}</p>
                </div>`;
                input.value = ""; // 清空輸入框
                showToast("解答完畢！", "fa-lightbulb");
            } catch (err) {
                container.innerHTML = `<p class="text-rose-400 font-bold"><i class="fa-solid fa-triangle-exclamation"></i> 回覆失敗：${err.message}</p>
                <p class="text-[10px] text-slate-500 mt-1">請稍候再試一次。</p>`;
            } finally {
                loader.classList.add('hidden');
                container.classList.remove('hidden');
            }
        }
    </script>
</body>
</html>
