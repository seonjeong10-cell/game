<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>탑텐 짝찾기 가치 게임</title>
    <style>
        :root {
            --primary: #6C5CE7;
            --secondary: #A29BFE;
            --accent: #00CEC9;
            --bg: #F0F2F5;
            --card-back: #6C5CE7;
            --card-front: #FFFFFF;
        }

        body {
            font-family: 'Pretendard', -apple-system, BlinkMacSystemFont, system-ui, Roboto, sans-serif;
            background-color: var(--bg);
            margin: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            color: #2D3436;
        }

        header {
            text-align: center;
            padding: 2rem;
        }

        h1 { color: var(--primary); margin-bottom: 0.5rem; }
        p { color: #636E72; font-size: 1.1rem; }

        .stats-container {
            display: flex;
            gap: 20px;
            margin-bottom: 20px;
            font-weight: bold;
            background: white;
            padding: 10px 20px;
            border-radius: 50px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }

        .game-board {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 15px;
            max-width: 800px;
            width: 95%;
            perspective: 1000px;
        }

        .card {
            aspect-ratio: 3/4;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
            cursor: pointer;
        }

        .card.flipped {
            transform: rotateY(180deg);
        }

        .card.matched {
            transform: rotateY(180deg) scale(0.95);
            opacity: 0.7;
            cursor: default;
        }

        .card-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 15px;
            box-sizing: border-box;
            border-radius: 12px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
            text-align: center;
            word-break: keep-all;
            font-weight: 600;
        }

        .card-front {
            background-color: var(--card-front);
            transform: rotateY(180deg);
            border: 3px solid var(--secondary);
            font-size: 0.9rem;
        }

        .card-back {
            background-color: var(--card-back);
            color: white;
            font-size: 2rem;
        }

        .card-back::after {
            content: "?";
        }

        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.8);
            justify-content: center;
            align-items: center;
            z-index: 100;
        }

        .modal-content {
            background: white;
            padding: 2rem;
            border-radius: 20px;
            max-width: 500px;
            width: 90%;
            text-align: center;
        }

        .checklist {
            text-align: left;
            margin: 20px 0;
            padding: 15px;
            background: #f8f9fa;
            border-radius: 10px;
        }

        .btn {
            background: var(--primary);
            color: white;
            border: none;
            padding: 12px 30px;
            border-radius: 25px;
            font-size: 1rem;
            cursor: pointer;
            transition: 0.3s;
        }

        .btn:hover { background: var(--secondary); }

        @media (max-width: 600px) {
            .game-board { grid-template-columns: repeat(3, 1fr); }
            .card-front { font-size: 0.75rem; }
        }
    </style>
</head>
<body>

<header>
    <h1>🌟 가치 짝꿍 찾기!</h1>
    <p>단어와 그 의미를 연결해 보세요. 당신은 어떤 가치를 소중히 여기나요?</p>
</header>

<div class="stats-container">
    <span>점수: <span id="score">0</span></span>
    <span>시도 횟수: <span id="attempts">0</span></span>
    <span>남은 시간: <span id="timer">60</span>s</span>
</div>

<main class="game-board" id="gameBoard"></main>

<div class="modal" id="resultModal">
    <div class="modal-content">
        <h2 id="resultTitle">축하합니다! 🎉</h2>
        <p id="resultText"></p>
        
        <div class="checklist">
            <h3>📝 스스로 돌아보기 (체크리스트)</h3>
            <div><input type="checkbox" id="c1"> <label for="c1">가치 단어의 뜻을 이해했나요? </label></div>
            <div><input type="checkbox" id="c2"> <label for="c2">끝까지 포기하지 않고 참여했나요? </label></div>
            <div><input type="checkbox" id="c3"> <label for="c3">새로운 단어를 배우는 도전을 즐겼나요? </label></div>
        </div>

        <button class="btn" onclick="location.reload()">다시 도전하기</button>
    </div>
</div>

<script>
    const valuesData = [
        { word: "공감", desc: "다른 사람의 기분을 같이 느끼는 마음 " },
        { word: "끈기", desc: "포기하지 않고 끝까지 노력함 " },
        { word: "도전", desc: "새로운 일에 용기있게 뛰어듦 " },
        { word: "우정", desc: "친구 사이에 나누는 두터운 사랑 " },
        { word: "인내", desc: "괴로움을 참고 견뎌내는 힘 " },
        { word: "자신감", desc: "스스로를 믿고 당당하게 행동함 " },
        { word: "창의성", desc: "새로운 것을 생각해 내는 능력 [cite: 1, 2]" },
        { word: "책임", desc: "내가 맡은 일을 끝까지 해내는 것 " },
        { word: "평화", desc: "싸움이나 전쟁이 없이 평온함 " },
        { word: "정직", desc: "속임 없이 정정당당하고 곧은 마음 " },
        { word: "협동", desc: "여럿이 힘을 합쳐 함께 일함 " },
        { word: "효도", desc: "부모님을 정성껏 모시는 도리 " },
        { word: "존중", desc: "상대를 높이 평가하고 소중히 여김 " },
        { word: "희망", desc: "앞날에 대해 갖는 밝은 기대 " },
        { word: "배려", desc: "남을 위해 따뜻하게 마음을 쓰는 것 " },
        { word: "성실", desc: "마음이 참되고 부지런하며 열심히 함 " },
        { word: "질서", desc: "혼란이 없이 정해진 순서대로 이루어짐 " },
        { word: "용기", desc: "씩씩하고 굳센 기운 " },
        { word: "예의", desc: "예로써 나타내는 존경의 말투나 몸가짐 " },
        { word: "긍정", desc: "옳다고 여기거나 좋다고 생각하는 태도 " },
        { word: "정의", desc: "올바르고 공정한 상태를 추구함 " },
        { word: "자주", desc: "자신이 주인이 되어 일을 스스로 처리함 " },
        { word: "감사", desc: "고맙게 여기는 마음 " }
    ];

    let cards = [];
    let flippedCards = [];
    let matchedPairs = 0;
    let attempts = 0;
    let score = 0;
    let timeLeft = 120;
    let timerInterval;

    function initGame() {
        // 데이터에서 랜덤하게 8쌍(16장) 선택
        const selected = [...valuesData].sort(() => 0.5 - Math.random()).slice(0, 8);
        const gameItems = [];
        
        selected.forEach(item => {
            gameItems.push({ type: 'word', content: item.word, id: item.word });
            gameItems.push({ type: 'desc', content: item.desc, id: item.word });
        });

        // 카드 섞기
        cards = gameItems.sort(() => 0.5 - Math.random());
        
        const board = document.getElementById('gameBoard');
        cards.forEach((item, index) => {
            const cardEl = document.createElement('div');
            cardEl.classList.add('card');
            cardEl.dataset.id = item.id;
            cardEl.dataset.index = index;
            
            cardEl.innerHTML = `
                <div class="card-face card-back"></div>
                <div class="card-face card-front">${item.content}</div>
            `;
            
            cardEl.addEventListener('click', flipCard);
            board.appendChild(cardEl);
        });

        startTimer();
    }

    function flipCard() {
        if (flippedCards.length === 2 || this.classList.contains('flipped') || this.classList.contains('matched')) return;

        this.classList.add('flipped');
        flippedCards.push(this);

        if (flippedCards.length === 2) {
            attempts++;
            document.getElementById('attempts').innerText = attempts;
            checkMatch();
        }
    }

    function checkMatch() {
        const [card1, card2] = flippedCards;
        const isMatch = card1.dataset.id === card2.dataset.id;

        if (isMatch) {
            card1.classList.add('matched');
            card2.classList.add('matched');
            matchedPairs++;
            score += 10;
            document.getElementById('score').innerText = score;
            flippedCards = [];
            if (matchedPairs === 8) endGame(true);
        } else {
            setTimeout(() => {
                card1.classList.remove('flipped');
                card2.classList.remove('flipped');
                flippedCards = [];
            }, 1000);
        }
    }

    function startTimer() {
        timerInterval = setInterval(() => {
            timeLeft--;
            document.getElementById('timer').innerText = timeLeft;
            if (timeLeft <= 0) endGame(false);
        }, 1000);
    }

    function endGame(isWin) {
        clearInterval(timerInterval);
        const modal = document.getElementById('resultModal');
        const title = document.getElementById('resultTitle');
        const text = document.getElementById('resultText');
        
        modal.style.display = 'flex';
        if (isWin) {
            title.innerText = "대단해요! 가치 마스터 등극! 🏆";
            text.innerText = `${attempts}번의 시도 끝에 모든 가치를 찾았습니다.`;
        } else {
            title.innerText = "시간이 다 됐어요! ⏰";
            text.innerText = "다시 한 번 도전해서 가치들을 연결해 볼까요?";
        }
    }

    initGame();
</script>

</body>
</html>
