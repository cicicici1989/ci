<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>儿童奖励抽奖器（手机版）</title>
    <style>
        /* 基础粉色系样式 */
        body {
            font-family: 'Comic Sans MS', cursive, sans-serif;
            text-align: center;
            background-color: #ffebee;
            padding: 10px;
            margin: 0;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            touch-action: manipulation; /* 禁用双击缩放 */
        }
        .container {
            width: 95%;
            max-width: 600px;
            margin: 0 auto;
            background-color: #fff;
            padding: 20px;
            border-radius: 20px;
            box-shadow: 0 5px 15px rgba(255, 105, 180, 0.2);
            border: 3px solid #ff69b4;
        }
        h1 {
            color: #ff1493;
            margin: 15px 0;
            font-size: clamp(24px, 6vw, 32px); /* 响应式标题 */
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
        }
        #prize-display {
            font-size: clamp(24px, 6vw, 32px);
            min-height: 120px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 20px 0;
            background-color: #fff0f5;
            border-radius: 15px;
            border: 3px dashed #ff69b4;
            color: #c71585;
            font-weight: bold;
            padding: 10px;
        }
        .button-container {
            display: flex;
            justify-content: center;
            gap: 15px;
            flex-wrap: wrap;
        }
        button {
            background-color: #ff69b4;
            color: white;
            border: none;
            padding: 15px 30px;
            font-size: clamp(18px, 5vw, 22px);
            cursor: pointer;
            border-radius: 50px;
            transition: all 0.3s;
            box-shadow: 0 4px 8px rgba(255, 105, 180, 0.3);
            -webkit-tap-highlight-color: transparent; /* 去除点击灰框 */
            flex-grow: 1;
            max-width: 200px;
        }
        button:hover, button:active {
            background-color: #ff1493;
            transform: scale(1.05);
        }
        button:disabled {
            background-color: #ccc;
            cursor: not-allowed;
            transform: none;
        }
        #stop-button {
            background-color: #ff4757;
            display: none;
        }
        #stop-button:hover {
            background-color: #ff0000;
        }
        
        /* 手机专属优化 */
        @media (max-width: 500px) {
            .container {
                padding: 15px;
            }
            button {
                padding: 18px 25px;
                min-width: 120px;
            }
            #prize-display {
                min-height: 100px;
                font-size: 28px;
            }
        }

        /* 动画效果 */
        .question-mark {
            animation: pulse 0.8s infinite alternate;
        }
        @keyframes pulse {
            from { transform: scale(1); }
            to { transform: scale(1.1); }
        }
        .confetti {
            position: fixed;
            width: 12px;
            height: 12px;
            background-color: #f00;
            border-radius: 50%;
            animation: fall 3s linear forwards;
            z-index: 1000;
        }
        @keyframes fall {
            to {
                transform: translateY(100vh) rotate(720deg);
                opacity: 0;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎁 儿童奖励抽奖器 🎁</h1>
        
        <div id="prize-display">
            <div id="prize-result">点击开始按钮抽奖</div>
        </div>
        
        <div class="button-container">
            <button id="start-button">开始</button>
            <button id="stop-button">停止</button>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const startButton = document.getElementById('start-button');
            const stopButton = document.getElementById('stop-button');
            const prizeResult = document.getElementById('prize-result');
            const prizeDisplay = document.getElementById('prize-display');
            
            // 奖品列表
            const prizes = [
                "免单卡 免单 1件",
                "小红书 流量卡3次",
                "自由卡 自由2小时",
                "玩具卡 30元",
                "玩具卡 50元",
                "零食卡 任意吃1次",
                "冰淇淋卡 任意吃1次",
                "陪伴卡 1小时",
                "讲个故事 1次",
                "乐园 1 次",
                "出游卡 定义去哪里玩 2天内"
            ];
            
            let isDrawing = false;
            let animationInterval;
            let speed = 100;
            let acceleration = 1.1;
            let currentPrize = "";
            
            // 手机震动反馈
            function vibrate() {
                if ("vibrate" in navigator) {
                    navigator.vibrate(50); // 震动50ms
                }
            }
            
            // 开始抽奖
            startButton.addEventListener('click', function() {
                if (isDrawing) return;
                
                vibrate();
                isDrawing = true;
                startButton.disabled = true;
                stopButton.style.display = 'block';
                prizeDisplay.classList.add('question-mark');
                prizeResult.textContent = "这是什么呢？";
                
                speed = 100;
                let counter = 0;
                
                animationInterval = setInterval(() => {
                    const randomIndex = Math.floor(Math.random() * prizes.length);
                    currentPrize = prizes[randomIndex];
                    
                    counter++;
                    if (counter % 5 === 0) {
                        clearInterval(animationInterval);
                        speed = Math.max(30, speed / acceleration);
                        animationInterval = setInterval(arguments.callee, speed);
                    }
                }, speed);
            });
            
            // 停止抽奖
            stopButton.addEventListener('click', function() {
                if (!isDrawing) return;
                
                vibrate();
                clearInterval(animationInterval);
                isDrawing = false;
                startButton.disabled = false;
                stopButton.style.display = 'none';
                prizeDisplay.classList.remove('question-mark');
                
                prizeResult.textContent = currentPrize || prizes[Math.floor(Math.random() * prizes.length)];
                createConfetti();
            });
            
            // 彩色纸屑效果
            function createConfetti() {
                const colors = ['#ff69b4', '#ff1493', '#ffb6c1', '#db7093', '#ffc0cb'];
                
                for (let i = 0; i < 50; i++) {
                    const confetti = document.createElement('div');
                    confetti.className = 'confetti';
                    confetti.style.left = Math.random() * 100 + 'vw';
                    confetti.style.backgroundColor = colors[Math.floor(Math.random() * colors.length)];
                    confetti.style.width = Math.random() * 10 + 8 + 'px';
                    confetti.style.height = confetti.style.width;
                    confetti.style.animationDuration = Math.random() * 2 + 2 + 's';
                    document.body.appendChild(confetti);
                    
                    setTimeout(() => {
                        confetti.remove();
                    }, 3000);
                }
            }
            
            // 防止手机端误触
            document.addEventListener('touchstart', function(e) {
                if (e.touches.length > 1) {
                    e.preventDefault();
                }
            }, { passive: false });
        });
    </script>
</body>
</html>

