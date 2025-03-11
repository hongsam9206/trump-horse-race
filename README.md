<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>트럼프 카드 경마</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; }
        .track { display: flex; flex-direction: column; align-items: center; position: relative; width: 80%; margin: auto; }
        .horse-container { display: flex; align-items: center; position: relative; width: 100%; height: 60px; }
        .horse img { width: 50px; height: auto; position: absolute; left: 0; transition: transform 0.3s ease-in-out; }
        .log { text-align: left; max-width: 400px; margin: 0 auto; height: 100px; overflow-y: auto; border: 1px solid #ccc; padding: 5px; }
    </style>
</head>
<body>
    <h1>🏇 트럼프 카드 경마 🏇</h1>
    <p>현재 보유 골드: <span id="gold">100</span>골드</p>

    <h3>🐴 말 정보 및 배당률</h3>
    <ul>
        <li>♥ - 불꽃 스프린터 (배당률: 1.5배, 속도 +3)</li>
        <li>♦ - 꾸준한 강자 (배당률: 2.0배, 속도 +2)</li>
        <li>♠ - 가속형 돌격마 (배당률: 2.5배, 속도 +1)</li>
        <li>♣ - 언더독 승부사 (배당률: 3.0배, 속도 +0)</li>
    </ul>

    <label for="horse">배팅할 말 선택:</label>
    <select id="horse">
        <option value="♥">♥</option>
        <option value="♦">♦</option>
        <option value="♠">♠</option>
        <option value="♣">♣</option>
    </select>
    
    <br>
    <label for="bet">배팅할 금액:</label>
    <input type="number" id="bet" min="1" value="10">
    <br>
    <button onclick="startRace()">배팅하기</button>

    <div class="track">
        <div class="horse-container"><img class="horse" id="horse_♥_img" src="images/horse1.png" alt="♥"></div>
        <div class="horse-container"><img class="horse" id="horse_♦_img" src="images/horse2.png" alt="♦"></div>
        <div class="horse-container"><img class="horse" id="horse_♠_img" src="images/horse3.png" alt="♠"></div>
        <div class="horse-container"><img class="horse" id="horse_♣_img" src="images/horse4.png" alt="♣"></div>
    </div>

    <h3>📜 최근 경주 기록 📜</h3>
    <div class="log" id="log"></div>

    <p id="result"></p>
    <button onclick="resetGame()">다시 하기</button>

    <script>
        let gold = 100;
        let odds = { "♥": 1.5, "♦": 2.0, "♠": 2.5, "♣": 3.0 }; 
        let speeds = { "♥": 3, "♦": 2, "♠": 1, "♣": 0 };
        let raceInProgress = false;
        let raceInterval;

        function startRace() {
            if (raceInProgress) return;
            raceInProgress = true;

            let betHorse = document.getElementById("horse").value;
            let betAmount = parseInt(document.getElementById("bet").value);

            if (betAmount > gold || betAmount <= 0) {
                alert("배팅 금액이 잘못되었습니다!");
                raceInProgress = false;
                return;
            }

            gold -= betAmount;
            document.getElementById("gold").textContent = gold;

            let positions = { "♥": 0, "♦": 0, "♠": 0, "♣": 0 };
            let logDiv = document.getElementById("log");
            logDiv.innerHTML = "";

            raceInterval = setInterval(() => {
                let movingHorse = Object.keys(positions)[Math.floor(Math.random() * 4)];
                positions[movingHorse] += Math.floor(Math.random() * 3) + 1 + speeds[movingHorse];
                document.getElementById("horse_" + movingHorse + "_img").style.transform = `translateX(${positions[movingHorse] * 20}px)`;
                logDiv.innerHTML = `📢 ${movingHorse} 카드가 나와서 ${movingHorse} 말이 전진! (현재 위치: ${positions[movingHorse]})<br>` + logDiv.innerHTML;

                let winner = Object.keys(positions).find(horse => positions[horse] >= 20);
                if (winner) {
                    clearInterval(raceInterval);
                    raceInProgress = false;
                    let resultText = `🎉 ${winner} 말이 우승했습니다! 🎉`;
                    if (winner === betHorse) {
                        let winnings = Math.floor(betAmount * odds[winner]);
                        gold += winnings;
                        resultText += `<br>💰 배팅 성공! ${winnings}골드 획득! 💰`;
                    } else {
                        resultText += "<br>😢 배팅 실패! 다음 기회에! 😢";
                    }
                    document.getElementById("gold").textContent = gold;
                    document.getElementById("result").innerHTML = resultText;
                }
            }, 300);
        }

        function resetGame() {
            clearInterval(raceInterval);
            raceInProgress = false;
            gold = 100;
            document.getElementById("gold").textContent = gold;
            document.getElementById("result").innerHTML = "";
            document.getElementById("log").innerHTML = "💡 게임이 초기화되었습니다!";
            for (let horse of ["♥", "♦", "♠", "♣"]) {
                document.getElementById("horse_" + horse + "_img").style.transform = "translateX(0px)";
            }
        }
    </script>
</body>
</html>
