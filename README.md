[index.html](https://github.com/user-attachments/files/26363508/index.html)
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>가치 짝꿍 찾기</title>
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;600;700;900&family=Gmarket+Sans:wght@700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #5C4FF6;
            --primary-light: #A29BFE;
            --accent: #FF6B6B;
            --gold: #FFD700;
            --silver: #C0C0C0;
            --bronze: #CD7F32;
            --bg: #0F0E1A;
            --surface: #1A1830;
            --surface2: #241F3A;
            --text: #F0EEFF;
            --text-muted: #8B82B8;
            --card-back: #2D2650;
            --success: #00E5A0;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }

        body {
            font-family: 'Noto Sans KR', sans-serif;
            background: var(--bg);
            color: var(--text);
            min-height: 100vh;
            overflow-x: hidden;
        }

        /* ── 배경 파티클 효과 ── */
        body::before {
            content: '';
            position: fixed;
            inset: 0;
            background:
                radial-gradient(ellipse 60% 40% at 20% 20%, rgba(92,79,246,0.15) 0%, transparent 60%),
                radial-gradient(ellipse 40% 60% at 80% 80%, rgba(255,107,107,0.08) 0%, transparent 60%);
            pointer-events: none;
            z-index: 0;
        }

        /* ════════════════════════════
           화면 전환
        ════════════════════════════ */
        .screen {
            display: none;
            position: relative;
            z-index: 1;
            min-height: 100vh;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 2rem 1rem;
            animation: fadeIn 0.5s ease;
        }
        .screen.active { display: flex; }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(16px); }
            to   { opacity: 1; transform: translateY(0); }
        }

        /* ════════════════════════════
           닉네임 입력 화면
        ════════════════════════════ */
        .nickname-screen {
            text-align: center;
        }

        .logo {
            font-family: 'Noto Sans KR', sans-serif;
            font-weight: 900;
            font-size: clamp(2rem, 6vw, 3.2rem);
            background: linear-gradient(135deg, #A29BFE, #FF6B6B);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            margin-bottom: 0.4rem;
            line-height: 1.1;
        }

        .subtitle {
            color: var(--text-muted);
            font-size: 1rem;
            margin-bottom: 2.5rem;
        }

        .nickname-card {
            background: var(--surface);
            border: 1px solid rgba(162,155,254,0.2);
            border-radius: 24px;
            padding: 2.5rem 2rem;
            max-width: 420px;
            width: 100%;
            box-shadow: 0 20px 60px rgba(0,0,0,0.4);
        }

        .nickname-card h2 {
            font-size: 1.1rem;
            color: var(--primary-light);
            margin-bottom: 1.2rem;
            font-weight: 700;
        }

        .input-wrap {
            position: relative;
            margin-bottom: 1.2rem;
        }

        .nickname-input {
            width: 100%;
            background: var(--surface2);
            border: 2px solid rgba(162,155,254,0.3);
            border-radius: 14px;
            color: var(--text);
            font-family: inherit;
            font-size: 1.2rem;
            font-weight: 700;
            padding: 1rem 1.2rem;
            outline: none;
            transition: border-color 0.2s;
            text-align: center;
        }

        .nickname-input:focus {
            border-color: var(--primary-light);
        }

        .nickname-input::placeholder { color: var(--text-muted); font-weight: 400; }

        .char-count {
            position: absolute;
            right: 14px;
            top: 50%;
            transform: translateY(-50%);
            font-size: 0.75rem;
            color: var(--text-muted);
        }

        .how-to {
            background: var(--surface2);
            border-radius: 14px;
            padding: 1rem 1.2rem;
            margin-bottom: 1.5rem;
            text-align: left;
        }

        .how-to-title {
            font-size: 0.8rem;
            font-weight: 700;
            color: var(--primary-light);
            margin-bottom: 0.6rem;
        }

        .how-to-steps {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .how-step {
            display: flex;
            align-items: center;
            gap: 10px;
            font-size: 0.82rem;
            color: var(--text-muted);
        }

        .how-step .num {
            width: 20px; height: 20px;
            background: var(--primary);
            border-radius: 50%;
            display: flex; align-items: center; justify-content: center;
            font-size: 0.7rem;
            font-weight: 700;
            color: white;
            flex-shrink: 0;
        }

        .btn-start {
            width: 100%;
            background: linear-gradient(135deg, var(--primary), #8B5CF6);
            color: white;
            border: none;
            border-radius: 14px;
            font-family: inherit;
            font-size: 1.1rem;
            font-weight: 700;
            padding: 1rem;
            cursor: pointer;
            transition: transform 0.15s, box-shadow 0.15s;
            box-shadow: 0 8px 24px rgba(92,79,246,0.4);
        }

        .btn-start:hover:not(:disabled) {
            transform: translateY(-2px);
            box-shadow: 0 12px 32px rgba(92,79,246,0.5);
        }

        .btn-start:disabled {
            opacity: 0.4;
            cursor: not-allowed;
        }

        /* ════════════════════════════
           게임 화면
        ════════════════════════════ */
        .game-screen {
            padding-top: 1rem;
            justify-content: flex-start;
        }

        .game-header {
            width: 100%;
            max-width: 840px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0.8rem 1rem;
            background: var(--surface);
            border-radius: 16px;
            margin-bottom: 1.2rem;
            border: 1px solid rgba(162,155,254,0.15);
        }

        .player-badge {
            display: flex;
            align-items: center;
            gap: 8px;
            font-weight: 700;
            font-size: 0.95rem;
        }

        .avatar {
            width: 32px; height: 32px;
            background: linear-gradient(135deg, var(--primary), #FF6B6B);
            border-radius: 50%;
            display: flex; align-items: center; justify-content: center;
            font-size: 1rem;
        }

        .stats {
            display: flex;
            gap: 16px;
        }

        .stat {
            text-align: center;
        }

        .stat-val {
            font-size: 1.2rem;
            font-weight: 900;
            color: var(--primary-light);
            display: block;
            line-height: 1;
        }

        .stat-label {
            font-size: 0.65rem;
            color: var(--text-muted);
        }

        .timer-val {
            color: var(--success) !important;
            transition: color 0.3s;
        }

        .timer-val.urgent { color: var(--accent) !important; animation: pulse 0.5s infinite alternate; }

        @keyframes pulse { from { opacity: 1; } to { opacity: 0.5; } }

        /* 게임 보드 */
        .game-board {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px;
            max-width: 840px;
            width: 100%;
            perspective: 1000px;
        }

        .card {
            aspect-ratio: 3/4;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.55s cubic-bezier(0.4, 0, 0.2, 1);
            cursor: pointer;
        }

        .card:hover:not(.flipped):not(.matched) .card-back {
            background: #3D3570;
            transform: scale(1.03);
        }

        .card.flipped { transform: rotateY(180deg); }

        .card.matched {
            transform: rotateY(180deg) scale(0.96);
            opacity: 0.55;
            cursor: default;
        }

        .card.matched .card-front {
            border-color: var(--success);
        }

        .card-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 12px;
            border-radius: 14px;
            text-align: center;
            word-break: keep-all;
            font-weight: 700;
            transition: transform 0.2s;
        }

        .card-back {
            background: var(--card-back);
            border: 2px solid rgba(162,155,254,0.2);
            font-size: 1.8rem;
            transition: background 0.2s, transform 0.2s;
        }

        .card-back::after { content: "?"; color: var(--primary-light); }

        .card-front {
            background: var(--surface);
            border: 2px solid rgba(162,155,254,0.35);
            transform: rotateY(180deg);
            font-size: clamp(0.7rem, 2vw, 0.88rem);
            color: var(--text);
            line-height: 1.4;
        }

        /* 매칭 애니메이션 */
        @keyframes matchPop {
            0%   { transform: rotateY(180deg) scale(1); }
            50%  { transform: rotateY(180deg) scale(1.1); }
            100% { transform: rotateY(180deg) scale(0.96); }
        }

        .card.match-anim { animation: matchPop 0.4s ease; }

        /* ════════════════════════════
           결과 화면
        ════════════════════════════ */
        .result-screen {
            text-align: center;
        }

        .result-title {
            font-weight: 900;
            font-size: clamp(1.6rem, 5vw, 2.4rem);
            margin-bottom: 0.4rem;
            background: linear-gradient(135deg, #FFD700, #FF6B6B);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .result-subtitle {
            color: var(--text-muted);
            font-size: 0.95rem;
            margin-bottom: 2rem;
        }

        .result-card {
            background: var(--surface);
            border: 1px solid rgba(162,155,254,0.2);
            border-radius: 24px;
            padding: 2rem;
            max-width: 480px;
            width: 100%;
            margin-bottom: 1.5rem;
            box-shadow: 0 20px 60px rgba(0,0,0,0.4);
        }

        .nickname-reveal {
            font-size: 1.8rem;
            font-weight: 900;
            color: var(--primary-light);
            margin-bottom: 1.5rem;
            padding: 1rem;
            background: var(--surface2);
            border-radius: 14px;
        }

        .score-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 12px;
            margin-bottom: 1.5rem;
        }

        .score-item {
            background: var(--surface2);
            border-radius: 12px;
            padding: 1rem 0.5rem;
        }

        .score-item .val {
            font-size: 1.6rem;
            font-weight: 900;
            display: block;
        }

        .score-item .lbl {
            font-size: 0.72rem;
            color: var(--text-muted);
        }

        .rank-badge {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            padding: 0.6rem 1.4rem;
            border-radius: 50px;
            font-weight: 700;
            font-size: 1rem;
            margin-bottom: 1.5rem;
        }

        .rank-1 { background: linear-gradient(135deg, #FFD700, #FFA500); color: #5d3a00; }
        .rank-2 { background: linear-gradient(135deg, #C0C0C0, #A0A0A0); color: #2a2a2a; }
        .rank-3 { background: linear-gradient(135deg, #CD7F32, #A0522D); color: white; }
        .rank-other { background: var(--surface2); color: var(--text-muted); }

        .checklist {
            text-align: left;
            background: var(--surface2);
            border-radius: 14px;
            padding: 1.2rem;
            margin-bottom: 1.5rem;
        }

        .checklist h3 {
            font-size: 0.85rem;
            color: var(--primary-light);
            margin-bottom: 0.8rem;
        }

        .check-item {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 8px;
            font-size: 0.85rem;
        }

        .check-item input[type="checkbox"] {
            width: 18px; height: 18px;
            accent-color: var(--primary-light);
            cursor: pointer;
            flex-shrink: 0;
        }

        .btn-retry {
            width: 100%;
            max-width: 480px;
            background: linear-gradient(135deg, var(--primary), #8B5CF6);
            color: white;
            border: none;
            border-radius: 14px;
            font-family: inherit;
            font-size: 1rem;
            font-weight: 700;
            padding: 1rem;
            cursor: pointer;
            transition: transform 0.15s, box-shadow 0.15s;
            box-shadow: 0 8px 24px rgba(92,79,246,0.4);
        }

        .btn-retry:hover {
            transform: translateY(-2px);
            box-shadow: 0 12px 32px rgba(92,79,246,0.5);
        }

        /* ── 모바일 ── */
        @media (max-width: 600px) {
            .game-board { grid-template-columns: repeat(4, 1fr); gap: 8px; }
            .card-front { font-size: 0.65rem; padding: 8px; }
            .stats { gap: 10px; }
        }

        @media (max-width: 420px) {
            .game-board { grid-template-columns: repeat(4, 1fr); gap: 6px; }
        }
    </style>
</head>
<body>

<!-- ══════════════════════════════
     화면 1: 닉네임 입력
══════════════════════════════ -->
<div class="screen nickname-screen active" id="screenNickname">
    <div class="logo">🌟 가치 짝꿍 찾기</div>
    <p class="subtitle">단어와 뜻을 연결해 가치를 발견해요!</p>

    <div class="nickname-card">
        <h2>👤 닉네임을 입력하세요</h2>

        <div class="input-wrap">
            <input
                class="nickname-input"
                id="nicknameInput"
                type="text"
                maxlength="8"
                placeholder="예) 행복한 김민준"
                autocomplete="off"
            >
            <span class="char-count" id="charCount">0/8</span>
        </div>

        <div class="how-to">
            <div class="how-to-title">📖 게임 방법</div>
            <div class="how-to-steps">
                <div class="how-step"><span class="num">1</span><span>카드를 클릭해 뒤집어요</span></div>
                <div class="how-step"><span class="num">2</span><span>가치 <b>단어</b>와 <b>뜻</b>이 맞는 카드를 찾아요</span></div>
                <div class="how-step"><span class="num">3</span><span>짝이 맞으면 <b>+10점!</b> 8쌍을 모두 찾으세요</span></div>
                <div class="how-step"><span class="num">4</span><span>⏱ 120초 안에 완성하면 승리!</span></div>
            </div>
        </div>

        <button class="btn-start" id="btnStart" disabled onclick="startGame()">
            🚀 게임 시작!
        </button>
    </div>
</div>

<!-- ══════════════════════════════
     화면 2: 게임
══════════════════════════════ -->
<div class="screen game-screen" id="screenGame">
    <div class="game-header">
        <div class="player-badge">
            <div class="avatar">🎮</div>
            <span id="displayNickname">—</span>
        </div>
        <div class="stats">
            <div class="stat">
                <span class="stat-val" id="scoreVal">0</span>
                <span class="stat-label">점수</span>
            </div>
            <div class="stat">
                <span class="stat-val" id="attemptsVal">0</span>
                <span class="stat-label">시도</span>
            </div>
            <div class="stat">
                <span class="stat-val timer-val" id="timerVal">120</span>
                <span class="stat-label">초 남음</span>
            </div>
        </div>
    </div>

    <main class="game-board" id="gameBoard"></main>
</div>

<!-- ══════════════════════════════
     화면 3: 결과
══════════════════════════════ -->
<div class="screen result-screen" id="screenResult">
    <div class="result-title" id="resultTitle">대단해요! 🏆</div>
    <p class="result-subtitle" id="resultSubtitle">모든 가치 짝꿍을 찾았어요!</p>

    <div class="result-card">
        <div class="nickname-reveal" id="revealNickname">—</div>
        <div id="rankBadge"></div>

        <div class="score-grid">
            <div class="score-item">
                <span class="val" id="finalScore" style="color: var(--primary-light);">0</span>
                <span class="lbl">점수</span>
            </div>
            <div class="score-item">
                <span class="val" id="finalAttempts" style="color: var(--accent);">0</span>
                <span class="lbl">시도 횟수</span>
            </div>
            <div class="score-item">
                <span class="val" id="finalTime" style="color: var(--success);">—</span>
                <span class="lbl">걸린 시간</span>
            </div>
        </div>

        <div class="checklist">
            <h3>📝 스스로 돌아보기</h3>
            <div class="check-item"><input type="checkbox" id="c1"><label for="c1">가치 단어의 뜻을 이해했나요?</label></div>
            <div class="check-item"><input type="checkbox" id="c2"><label for="c2">끝까지 포기하지 않고 참여했나요?</label></div>
            <div class="check-item"><input type="checkbox" id="c3"><label for="c3">새로운 단어를 배우는 도전을 즐겼나요?</label></div>
        </div>
    </div>

    <button class="btn-retry" onclick="location.reload()">🔄 다시 도전하기</button>
</div>

<script>
// ── 데이터 ──
const valuesData = [
    { word: "공감", desc: "다른 사람의 기분을 같이 느끼는 마음" },
    { word: "끈기", desc: "포기하지 않고 끝까지 노력함" },
    { word: "도전", desc: "새로운 일에 용기있게 뛰어듦" },
    { word: "우정", desc: "친구 사이에 나누는 두터운 사랑" },
    { word: "인내", desc: "괴로움을 참고 견뎌내는 힘" },
    { word: "자신감", desc: "스스로를 믿고 당당하게 행동함" },
    { word: "창의성", desc: "새로운 것을 생각해 내는 능력" },
    { word: "책임", desc: "내가 맡은 일을 끝까지 해내는 것" },
    { word: "평화", desc: "싸움이나 전쟁이 없이 평온함" },
    { word: "정직", desc: "속임 없이 정정당당하고 곧은 마음" },
    { word: "협동", desc: "여럿이 힘을 합쳐 함께 일함" },
    { word: "효도", desc: "부모님을 정성껏 모시는 도리" },
    { word: "존중", desc: "상대를 높이 평가하고 소중히 여김" },
    { word: "희망", desc: "앞날에 대해 갖는 밝은 기대" },
    { word: "배려", desc: "남을 위해 따뜻하게 마음을 쓰는 것" },
    { word: "성실", desc: "마음이 참되고 부지런하며 열심히 함" },
    { word: "질서", desc: "혼란이 없이 정해진 순서대로 이루어짐" },
    { word: "용기", desc: "씩씩하고 굳센 기운" },
    { word: "예의", desc: "예로써 나타내는 존경의 말투나 몸가짐" },
    { word: "긍정", desc: "옳다고 여기거나 좋다고 생각하는 태도" },
    { word: "정의", desc: "올바르고 공정한 상태를 추구함" },
    { word: "자주", desc: "자신이 주인이 되어 일을 스스로 처리함" },
    { word: "감사", desc: "고맙게 여기는 마음" }
];

// ── 순위 localStorage 관리 ──
const RANK_KEY = 'valueGame_ranks_v1';

function getRanks() {
    try { return JSON.parse(localStorage.getItem(RANK_KEY)) || []; }
    catch { return []; }
}

function saveRank(entry) {
    const ranks = getRanks();
    ranks.push(entry);
    ranks.sort((a, b) => {
        if (b.score !== a.score) return b.score - a.score;
        return a.timeUsed - b.timeUsed; // 같은 점수면 빠른 사람이 앞
    });
    localStorage.setItem(RANK_KEY, JSON.stringify(ranks.slice(0, 50)));
    return ranks.findIndex(r => r === entry || (r.nickname === entry.nickname && r.score === entry.score && r.timeUsed === entry.timeUsed)) + 1;
}

function getMyRank(entry) {
    const ranks = getRanks();
    const idx = ranks.findIndex(r => r.nickname === entry.nickname && r.score === entry.score && r.timeUsed === entry.timeUsed);
    return idx + 1;
}

// ── 닉네임 입력 ──
const nicknameInput = document.getElementById('nicknameInput');
const btnStart = document.getElementById('btnStart');
const charCount = document.getElementById('charCount');

nicknameInput.addEventListener('input', () => {
    const v = nicknameInput.value.trim();
    charCount.textContent = `${nicknameInput.value.length}/8`;
    btnStart.disabled = v.length === 0;
});

nicknameInput.addEventListener('keydown', e => {
    if (e.key === 'Enter' && nicknameInput.value.trim()) startGame();
});

// ── 게임 상태 ──
let nickname = '';
let flippedCards = [];
let matchedPairs = 0;
let attempts = 0;
let score = 0;
let timeLeft = 120;
let timerInterval = null;
let canFlip = true;
let gameStartTime = null;

function showScreen(id) {
    document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
    document.getElementById(id).classList.add('active');
}

function startGame() {
    nickname = nicknameInput.value.trim();
    if (!nickname) return;

    document.getElementById('displayNickname').textContent = nickname;
    showScreen('screenGame');
    initBoard();
    startTimer();
    gameStartTime = Date.now();
}

function initBoard() {
    const selected = [...valuesData].sort(() => 0.5 - Math.random()).slice(0, 8);
    const items = [];
    selected.forEach(item => {
        items.push({ content: item.word, id: item.word });
        items.push({ content: item.desc, id: item.word });
    });
    items.sort(() => 0.5 - Math.random());

    const board = document.getElementById('gameBoard');
    board.innerHTML = '';

    items.forEach(item => {
        const card = document.createElement('div');
        card.className = 'card';
        card.dataset.id = item.id;
        card.innerHTML = `
            <div class="card-face card-back"></div>
            <div class="card-face card-front">${item.content}</div>
        `;
        card.addEventListener('click', onCardClick);
        board.appendChild(card);
    });
}

function onCardClick() {
    if (!canFlip || flippedCards.length >= 2 || this.classList.contains('flipped') || this.classList.contains('matched')) return;
    this.classList.add('flipped');
    flippedCards.push(this);
    if (flippedCards.length === 2) {
        attempts++;
        document.getElementById('attemptsVal').textContent = attempts;
        canFlip = false;
        checkMatch();
    }
}

function checkMatch() {
    const [a, b] = flippedCards;
    if (a.dataset.id === b.dataset.id) {
        a.classList.add('matched', 'match-anim');
        b.classList.add('matched', 'match-anim');
        matchedPairs++;
        score += 10;
        document.getElementById('scoreVal').textContent = score;
        flippedCards = [];
        canFlip = true;
        if (matchedPairs === 8) endGame(true);
    } else {
        setTimeout(() => {
            a.classList.remove('flipped');
            b.classList.remove('flipped');
            flippedCards = [];
            canFlip = true;
        }, 900);
    }
}

function startTimer() {
    timerInterval = setInterval(() => {
        timeLeft--;
        const el = document.getElementById('timerVal');
        el.textContent = timeLeft;
        if (timeLeft <= 20) el.classList.add('urgent');
        if (timeLeft <= 0) endGame(false);
    }, 1000);
}

function endGame(isWin) {
    clearInterval(timerInterval);
    const timeUsed = Math.round((Date.now() - gameStartTime) / 1000);

    const entry = { nickname, score, attempts, timeUsed, isWin, date: Date.now() };
    saveRank(entry);
    const myRank = getMyRank(entry);

    showScreen('screenResult');

    if (isWin) {
        document.getElementById('resultTitle').textContent = '대단해요! 🏆';
        document.getElementById('resultSubtitle').textContent = '모든 가치 짝꿍을 찾았어요!';
    } else {
        document.getElementById('resultTitle').textContent = '시간 초과! ⏰';
        document.getElementById('resultSubtitle').textContent = `${matchedPairs}쌍을 찾았어요. 다시 도전해 볼까요?`;
    }

    document.getElementById('revealNickname').textContent = `👤 ${nickname}`;
    document.getElementById('finalScore').textContent = score;
    document.getElementById('finalAttempts').textContent = attempts;
    document.getElementById('finalTime').textContent = isWin ? `${timeUsed}초` : '—';

    // 순위 배지
    const badge = document.getElementById('rankBadge');
    if (isWin) {
        let cls = 'rank-other', txt = `🎮 ${myRank}위`;
        if (myRank === 1) { cls = 'rank-1'; txt = '🥇 1등!'; }
        else if (myRank === 2) { cls = 'rank-2'; txt = '🥈 2등!'; }
        else if (myRank === 3) { cls = 'rank-3'; txt = '🥉 3등!'; }
        badge.innerHTML = `<div class="rank-badge ${cls}">${txt} (같은 기기 내 기록)</div>`;
    } else {
        badge.innerHTML = `<div class="rank-badge rank-other">💪 포기하지 말고 재도전!</div>`;
    }
}
</script>
</body>
</html>
