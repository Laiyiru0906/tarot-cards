# tarot-cards
塔羅牌簡單設計





<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>塔羅牌線上線上占卜系統 - 單頁實作版</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* 3D 卡牌翻轉動畫效果 */
        .perspective-1000 {
            perspective: 1000px;
        }
        .transform-style-3d {
            transform-style: preserve-3d;
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .rotate-y-180 {
            transform: rotateY(180deg);
        }
        .backface-hidden {
            backface-visibility: hidden;
        }
        
        /* 洗牌動畫 */
        @keyframes shuffleAnim {
            0% { transform: translateX(0) rotate(0deg); }
            25% { transform: translateX(-30px) rotate(-5deg); }
            50% { transform: translateX(30px) rotate(5deg); }
            75% { transform: translateX(-15px) rotate(-2deg); }
            100% { transform: translateX(0) rotate(0deg); }
        }
        .shuffling {
            animation: shuffleAnim 0.4s ease-in-out infinite;
        }
    </style>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen font-sans pb-12">

    <!-- Header -->
    <header class="border-b border-slate-800 bg-slate-900/80 backdrop-blur sticky top-0 z-50">
        <div class="max-w-5xl mx-auto px-4 py-4 flex justify-between items-center">
            <h1 class="text-xl font-bold bg-gradient-to-r from-purple-400 to-amber-300 bg-clip-text text-transparent">
                🔮 塔羅心靈探尋 (Tarot Oracle)
            </h1>
            <span class="text-xs bg-purple-950 text-purple-300 px-2.5 py-1 rounded-full border border-purple-800">
                SA/SD 範例系統 v1.0
            </span>
        </div>
    </header>

    <main class="max-w-4xl mx-auto px-4 mt-8 space-y-8">

        <!-- Step 1: 輸入問題與選擇牌陣 -->
        <section class="bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-xl">
            <h2 class="text-lg font-semibold text-purple-300 mb-4 flex items-center gap-2">
                <span>1.</span> 設定占卜情境
            </h2>
            <div class="space-y-4">
                <div>
                    <label class="block text-sm text-slate-400 mb-1">請輸入你心中的問題：</label>
                    <input type="text" id="questionInput" placeholder="例如：我近期的事業發展趨勢如何？" 
                           class="w-full bg-slate-950 border border-slate-700 rounded-xl px-4 py-3 text-slate-100 focus:outline-none focus:border-purple-500 focus:ring-1 focus:ring-purple-500 transition">
                </div>
                <div>
                    <label class="block text-sm text-slate-400 mb-1">選擇牌陣：</label>
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                        <button type="button" onclick="selectSpread('single')" id="btn-spread-single"
                                class="spread-btn border-2 border-purple-600 bg-purple-950/40 p-3 rounded-xl text-left transition flex justify-between items-center">
                            <div>
                                <div class="font-medium text-purple-200">單張牌 (Single Card)</div>
                                <div class="text-xs text-slate-400">快速解答 / 每日指引</div>
                            </div>
                            <span class="text-xs bg-purple-900/80 text-purple-200 px-2 py-0.5 rounded">1 張</span>
                        </button>
                        <button type="button" onclick="selectSpread('three')" id="btn-spread-three"
                                class="spread-btn border-2 border-slate-800 bg-slate-950 p-3 rounded-xl text-left transition flex justify-between items-center opacity-70 hover:opacity-100">
                            <div>
                                <div class="font-medium text-slate-300">三張牌陣 (Time Line)</div>
                                <div class="text-xs text-slate-400">過去、現在、未來時間線</div>
                            </div>
                            <span class="text-xs bg-slate-800 text-slate-300 px-2 py-0.5 rounded">3 張</span>
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- Step 2: 洗牌與抽牌互動區 -->
        <section class="bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-xl text-center">
            <h2 class="text-lg font-semibold text-purple-300 mb-4 flex items-center justify-center gap-2">
                <span>2.</span> 洗牌與抽取卡牌
            </h2>

            <div id="deckContainer" class="py-8 flex justify-center items-center h-48">
                <!-- 洗牌視覺動畫疊卡區 -->
                <div id="cardDeck" class="relative w-28 h-44 cursor-pointer group" onclick="handleDraw()">
                    <div class="deck-card absolute inset-0 bg-gradient-to-br from-indigo-900 to-purple-950 border-2 border-amber-500/40 rounded-xl shadow-lg flex items-center justify-center transform -rotate-3 transition group-hover:-rotate-6">
                        <span class="text-amber-400/50 text-2xl">✨</span>
                    </div>
                    <div class="deck-card absolute inset-0 bg-gradient-to-br from-indigo-900 to-purple-950 border-2 border-amber-500/40 rounded-xl shadow-lg flex items-center justify-center transform rotate-2 transition group-hover:rotate-4">
                        <span class="text-amber-400/50 text-2xl">☪</span>
                    </div>
                    <div class="deck-card absolute inset-0 bg-gradient-to-br from-indigo-900 to-purple-950 border-2 border-amber-500/60 rounded-xl shadow-xl flex items-center justify-center transform group-hover:scale-105 transition">
                        <div class="border border-amber-400/30 w-20 h-36 rounded-lg flex items-center justify-center">
                            <span class="text-amber-300 font-serif text-sm">點擊抽牌</span>
                        </div>
                    </div>
                </div>
            </div>

            <div class="flex justify-center gap-4 mt-2">
                <button onclick="shuffleDeck()" id="shuffleBtn" class="bg-slate-800 hover:bg-slate-700 text-slate-200 px-5 py-2.5 rounded-xl font-medium text-sm transition border border-slate-700">
                    🔀 重洗牌組 (Shuffle)
                </button>
            </div>
        </section>

        <!-- Step 3: 牌陣展示區 (Drawn Cards Display) -->
        <section id="resultsSection" class="hidden bg-slate-900 border border-slate-800 rounded-2xl p-6 shadow-xl">
            <h2 class="text-lg font-semibold text-purple-300 mb-6 flex items-center gap-2">
                <span>3.</span> 抽出的牌陣
            </h2>
            <div id="cardsDisplayGrid" class="grid grid-cols-1 sm:grid-cols-3 gap-6 justify-items-center">
                <!-- 動態注入翻牌卡片 -->
            </div>
        </section>

        <!-- Step 4: AI/系統故事解讀 (Narrative Analysis) -->
        <section id="narrativeSection" class="hidden bg-gradient-to-br from-slate-900 via-purple-950/20 to-slate-900 border border-purple-900/50 rounded-2xl p-6 shadow-2xl">
            <h2 class="text-lg font-semibold text-amber-300 mb-3 flex items-center gap-2">
                <span>4.</span> 心靈牌義解讀 (Narrative Analysis)
            </h2>
            <div id="narrativeContent" class="text-slate-300 leading-relaxed space-y-4 text-sm sm:text-base">
                <!-- 動態生成的綜合解讀 -->
            </div>
        </section>

    </main>

    <script>
        // ==========================================
        // 1. Data Store: 塔羅大阿爾克那資料集 (22張)
        // ==========================================
        const TAROT_DECK = [
            { id: 0, name: "愚者 (The Fool)", element: "風", upright: "新的開始、冒險、自由、無限可能", reversed: "輕率、缺乏準備、風險過高、盲目" },
            { id: 1, name: "魔術師 (The Magician)", element: "風", upright: "創造力、技能、專注、實現目標的資源", reversed: "意志力不集、欺騙、資源浪費、延誤" },
            { id: 2, name: "女祭司 (The High Priestess)", element: "水", upright: "直覺、潛意識、智慧、等待時機", reversed: "壓抑情緒、膚淺、忽視直覺、秘密被揭發" },
            { id: 3, name: "皇后 (The Empress)", element: "地", upright: "豐盛、滋養、母性、創造力與美", reversed: "成長受阻、過度依賴、浪費、創造力匱乏" },
            { id: 4, name: "皇帝 (The Emperor)", element: "火", upright: "結構、權威、控制、穩定與秩序", reversed: "控制欲過強、固執、缺乏紀律、權力衝突" },
            { id: 5, name: "教皇 (The Hierophant)", element: "地", upright: "傳統、信仰、指引、符合社會規範", reversed: "打破傳統、僵化、盲從、非傳統方式" },
            { id: 6, name: "戀人 (The Lovers)", element: "風", upright: "愛、選擇、夥伴關係、價值觀一致", reversed: "關係不和諧、錯誤抉擇、價值觀衝突" },
            { id: 7, name: "戰車 (The Chariot)", element: "水", upright: "意志力、克服障礙、勝利、方向感", reversed: "失去控制、方向不明、衝動導致失敗" },
            { id: 8, name: "力量 (Strength)", element: "火", upright: "勇氣、包容、內在力量、溫和控制", reversed: "自卑、缺乏耐性、被情緒吞噬" },
            { id: 9, name: "隱士 (The Hermit)", element: "地", upright: "反思、內省、尋求真理、獨處", reversed: "過度孤立、逃避現實、感到寂寞" },
            { id: 10, name: "命運之輪 (Wheel of Fortune)", element: "火", upright: "轉折點、好運、命運週期、改變", reversed: "抵抗改變、短暫挫折、不順遂" },
            { id: 11, name: "正義 (Justice)", element: "風", upright: "公平、因果、理性決策、誠實", reversed: "不公正、偏見、逃避責任、延遲" },
            { id: 12, name: "倒吊人 (The Hanged Man)", element: "水", upright: "換位思考、犧牲、等待、新的視角", reversed: "無謂的犧牲、拖延、無能為力" },
            { id: 13, name: "死神 (Death)", element: "水", upright: "結束、重生、轉變、淘汰舊事物", reversed: "抗拒改變、恐懼結束、停滯不前" },
            { id: 14, name: "節制 (Temperance)", element: "風", upright: "平衡、調和、耐心、適度與融合", reversed: "失去平衡、過度極端、缺乏溝通" },
            { id: 15, name: "惡魔 (The Devil)", element: "地", upright: "束縛、慾望、物質沉迷、執著", reversed: "擺脫束縛、覺醒、打破執念" },
            { id: 16, name: "高塔 (The Tower)", element: "火", upright: "突發劇變、信念崩解、覺醒、真相大白", reversed: "抗拒必然的改變、緊抱不放、危機延遲" },
            { id: 17, name: "星星 (The Star)", element: "風", upright: "希望、靈感、治癒、信心與願景", reversed: "絕望、失去信心、缺乏靈感" },
            { id: 18, name: "月亮 (The Moon)", element: "水", upright: "不安、幻覺、直覺、隱藏的恐懼", reversed: "迷霧散去、解開誤會、克服恐懼" },
            { id: 19, name: "太陽 (The Sun)", element: "火", upright: "成功、快樂、活力、明確與成功", reversed: "暫時的陰霾、過度樂觀、成功受阻" },
            { id: 20, name: "審判 (Judgement)", element: "火", upright: "召喚、覺醒、自我評估、重大決定", reversed: "自我懷疑、逃避審判、缺乏決斷" },
            { id: 21, name: "世界 (The World)", element: "土", upright: "圓滿、完成、成就、完美的旅程結束", reversed: "未竟之業、缺乏收尾、延遲達成" }
        ];

        // 牌陣定義
        const SPREADS = {
            single: {
                name: "單張牌",
                count: 1,
                positions: ["現狀與核心建議"]
            },
            three: {
                name: "三張牌陣",
                count: 3,
                positions: ["過去的影響 (Past)", "現在的狀況 (Present)", "未來的趨勢 (Future)"]
            }
        };

        // ==========================================
        // 2. Application State (應用程式狀態)
        // ==========================================
        let currentSpreadKey = 'single';
        let currentDeck = [...TAROT_DECK];
        let drawnCards = [];

        // ==========================================
        // 3. Logic & Event Handlers
        // ==========================================

        // 切換牌陣選擇
        function selectSpread(spreadKey) {
            currentSpreadKey = spreadKey;
            
            // 更新 UI 樣式
            document.querySelectorAll('.spread-btn').forEach(btn => {
                btn.classList.remove('border-purple-600', 'bg-purple-950/40');
                btn.classList.add('border-slate-800', 'bg-slate-950', 'opacity-70');
            });

            const activeBtn = document.getElementById(`btn-spread-${spreadKey}`);
            activeBtn.classList.add('border-purple-600', 'bg-purple-950/40');
            activeBtn.classList.remove('border-slate-800', 'bg-slate-950', 'opacity-70');

            resetDrawing();
        }

        // 洗牌演算法 (Fisher-Yates Shuffle)
        function shuffleDeck() {
            const deckEl = document.getElementById('cardDeck');
            deckEl.classList.add('shuffling');

            setTimeout(() => {
                currentDeck = [...TAROT_DECK];
                for (let i = currentDeck.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [currentDeck[i], currentDeck[j]] = [currentDeck[j], currentDeck[i]];
                }
                deckEl.classList.remove('shuffling');
                resetDrawing();
            }, 600);
        }

        // 重置抽牌結果
        function resetDrawing() {
            drawnCards = [];
            document.getElementById('resultsSection').classList.add('hidden');
            document.getElementById('narrativeSection').classList.add('hidden');
            document.getElementById('cardsDisplayGrid').innerHTML = '';
        }

        // 執行抽牌
        function handleDraw() {
            const spread = SPREADS[currentSpreadKey];
            
            // 如果牌組剩餘量不足，重洗
            if (currentDeck.length < spread.count) {
                currentDeck = [...TAROT_DECK];
            }

            drawnCards = [];
            // 隨機抽牌並決定正逆位
            for (let i = 0; i < spread.count; i++) {
                const card = currentDeck.pop();
                const isReversed = Math.random() < 0.5; // 50% 機率逆位
                drawnCards.push({
                    card: card,
                    positionName: spread.positions[i],
                    isReversed: isReversed
                });
            }

            renderDrawnCards();
            generateNarrative();
        }

        // 渲染抽出的卡牌 (含 3D 翻牌動畫)
        function renderDrawnCards() {
            const grid = document.getElementById('cardsDisplayGrid');
            grid.innerHTML = '';
            document.getElementById('resultsSection').classList.remove('hidden');

            drawnCards.forEach((item, index) => {
                const cardNode = document.createElement('div');
                cardNode.className = 'w-48 flex flex-col items-center';

                const orientationText = item.isReversed ? '【逆位】' : '【正位】';
                const orientationColor = item.isReversed ? 'text-amber-400' : 'text-emerald-400';
                const keywords = item.isReversed ? item.card.reversed : item.card.upright;

                cardNode.innerHTML = `
                    <div class="text-xs text-purple-300 font-semibold mb-2 bg-purple-950/60 px-3 py-1 rounded-full border border-purple-800/50">
                        ${item.positionName}
                    </div>
                    <div class="w-40 h-64 perspective-1000 cursor-pointer" onclick="flipCard(this)">
                        <div class="card-inner relative w-full h-full transform-style-3d shadow-2xl rounded-xl">
                            <!-- 牌背 (Front layer before flip) -->
                            <div class="absolute inset-0 bg-gradient-to-br from-indigo-950 to-purple-950 border-2 border-amber-500/50 rounded-xl flex items-center justify-center backface-hidden p-2">
                                <div class="border border-amber-400/30 w-full h-full rounded-lg flex items-center justify-center">
                                    <span class="text-amber-300 font-serif text-xs">點擊翻牌</span>
                                </div>
                            </div>
                            <!-- 牌面 (Back layer after flip) -->
                            <div class="absolute inset-0 bg-slate-800 border-2 border-purple-500/80 rounded-xl p-3 flex flex-col justify-between backface-hidden rotate-y-180 bg-gradient-to-b from-slate-800 to-slate-900">
                                <div class="text-right text-xs text-slate-400 font-mono">${item.card.element}元素</div>
                                <div class="text-center py-4 ${item.isReversed ? 'transform rotate-180' : ''}">
                                    <div class="text-3xl mb-1">🃏</div>
                                    <div class="font-bold text-sm text-slate-100">${item.card.name}</div>
                                </div>
                                <div class="text-center border-t border-slate-700 pt-2">
                                    <span class="text-xs font-bold ${orientationColor}">${orientationText}</span>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="mt-3 text-center text-xs text-slate-400 px-2">
                        <p class="text-slate-300 font-medium">${item.card.name}</p>
                        <p class="mt-1">${keywords}</p>
                    </div>
                `;
                grid.appendChild(cardNode);
            });

            // 自動觸發第一個卡牌翻轉效果
            setTimeout(() => {
                const firstCard = grid.querySelector('.card-inner');
                if (firstCard) firstCard.classList.add('rotate-y-180');
            }, 300);
        }

        // 翻牌點擊互動
        function flipCard(element) {
            const cardInner = element.querySelector('.card-inner');
            cardInner.classList.toggle('rotate-y-180');
        }

        // ==========================================
        // 4. Narrative Engine (動態心靈故事生成器)
        // ==========================================
        function generateNarrative() {
            const question = document.getElementById('questionInput').value.trim() || "近期的心靈與整體狀態";
            const narrativeBox = document.getElementById('narrativeContent');
            const container = document.getElementById('narrativeSection');

            let storyHtml = `<p class="font-medium text-purple-200">針對你所詢問的「<span class="text-amber-300">${question}</span>」，牌面呈現出以下的訊息指引：</p>`;

            if (currentSpreadKey === 'single') {
                const { card, isReversed } = drawnCards[0];
                const state = isReversed ? '逆位' : '正位';
                const keywords = isReversed ? card.reversed : card.upright;
                
                storyHtml += `
                    <p>抽出的關鍵牌卡為 <strong>${card.name}（${state}）</strong>。</p>
                    <p class="bg-slate-950/60 p-4 rounded-xl border-l-4 border-amber-400">
                        目前核心的能量焦點落在「<strong>${keywords}</strong>」。這意味著你當前不需要急於向外尋求解答，而是需要重新審視這個主題背後帶給你的課題。
                        ${isReversed ? '逆位的出現提醒你，可能存在某種內在的抵抗或盲點，需要放慢腳步來解開。' : '正位展現了強大的順應能量，代表時機已經成熟或方向大致正確。'}
                    </p>
                `;
            } else if (currentSpreadKey === 'three') {
                const [p1, p2, p3] = drawnCards;
                storyHtml += `
                    <div class="space-y-3">
                        <p>🔹 <strong>過去 (Past) - ${p1.card.name} (${p1.isReversed ? '逆位' : '正位'})：</strong> 根源來自於「${p1.isReversed ? p1.card.reversed : p1.card.upright}」，這是奠定你目前局面的基礎。</p>
                        <p>🔹 <strong>現在 (Present) - ${p2.card.name} (${p2.isReversed ? '逆位' : '正位'})：</strong> 目前處於「${p2.isReversed ? p2.card.reversed : p2.card.upright}」的狀態，這是你此刻需要直接面對的核心議題。</p>
                        <p>🔹 <strong>未來 (Future) - ${p3.card.name} (${p3.isReversed ? '逆位' : '正位'})：</strong> 演變趨勢指向「${p3.isReversed ? p3.card.reversed : p3.card.upright}」，為你指明了未來的潛在發展方向。</p>
                    </div>
                    <p class="bg-slate-950/60 p-4 rounded-xl border-l-4 border-purple-400 mt-3">
                        💡 <strong>綜合建議：</strong> 從過去到未來的能量轉變顯示，關鍵在於如何轉化「${p2.card.name}」所帶來的當前考驗。保持對內在直覺的察覺，你將能更順暢地迎向未來的轉折。
                    </p>
                `;
            }

            narrativeBox.innerHTML = storyHtml;
            container.classList.remove('hidden');
        }

        // 初始化預設洗牌
        shuffleDeck();
    </script>
</body>
</html>
