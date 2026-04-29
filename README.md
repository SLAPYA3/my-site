<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>SLAPYA_TV | LIVE</title>
    <!-- Подключаем альтернативные ссылки (зеркала) -->
    <script src="https://jsdelivr.net"></script>
    <script src="https://jsdelivr.net"></script>
    <style>
        body { background: #000; color: #fff; font-family: sans-serif; text-align: center; margin: 0; padding-top: 50px; }
        .card { background: #111; border: 2px solid #ff004d; border-radius: 15px; width: 90%; max-width: 500px; margin: 0 auto; padding: 20px; box-shadow: 0 0 20px rgba(255, 0, 77, 0.3); }
        #status { font-size: 14px; margin-bottom: 15px; font-weight: bold; }
        #chatWindow { height: 300px; overflow-y: auto; background: #050505; border-radius: 10px; padding: 10px; margin-bottom: 15px; text-align: left; border: 1px solid #222; }
        .msg { background: #ff004d; padding: 8px 12px; border-radius: 10px; margin-bottom: 8px; display: inline-block; clear: both; float: right; font-size: 14px; color: white; }
        input { width: 100%; padding: 12px; border-radius: 8px; border: 1px solid #333; background: #222; color: #fff; box-sizing: border-box; outline: none; }
        button { width: 100%; padding: 12px; background: #ff004d; color: #fff; border: none; border-radius: 8px; margin-top: 10px; font-weight: bold; cursor: pointer; text-transform: uppercase; }
    </style>
</head>
<body>

    <div class="card">
        <div style="font-size: 24px; font-weight: bold; margin-bottom: 10px;">SLAPYA<span>_TV</span></div>
        <div id="status" style="color: #ffcc00;">ПОДКЛЮЧЕНИЕ К ЭФИРУ...</div>
        
        <div id="chatWindow"></div>
        
        <input type="text" id="chatInput" placeholder="Введите сообщение..." onkeypress="if(event.keyCode==13) sendMsg()">
        <button onclick="sendMsg()">В ЭФИР 📡</button>
    </div>

<script>
    // ТВОИ ДАННЫЕ
    const firebaseConfig = {
        apiKey: "AIzaSyBCUf9EeU4Imh8kzHto2rNor-P_bgjpeWU",
        authDomain: "://firebaseapp.com",
        databaseURL: "https://slapya-tv-default-rtdb.firebaseio.com",
        projectId: "slapya-tv"
    };

    function start() {
        if (typeof firebase === 'undefined') {
            document.getElementById("status").innerText = "ОШИБКА: БЛОКИРОВКА СЕТИ ❌";
            document.getElementById("status").style.color = "red";
            return;
        }

        firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        // Проверка связи
        db.ref(".info/connected").on("value", (snap) => {
            const stat = document.getElementById("status");
            if (snap.val() === true) {
                stat.innerText = "СИГНАЛ: ЕСТЬ! ✅";
                stat.style.color = "#00ff00";
            } else {
                stat.innerText = "СИГНАЛ: ПОИСК... ⏳";
                stat.style.color = "#ffcc00";
            }
        });

        // Отправка
        window.sendMsg = function() {
            const input = document.getElementById('chatInput');
            if (!input.value.trim()) return;
            db.ref('global_chat').push({ m: input.value });
            input.value = "";
        };

        // Прием
        db.ref('global_chat').limitToLast(20).on('child_added', (snap) => {
            const win = document.getElementById('chatWindow');
            const div = document.createElement('div');
            div.className = 'msg';
            div.innerText = snap.val().m;
            win.appendChild(div);
            win.scrollTop = win.scrollHeight;
        });
    }

    // Запускаем с задержкой, чтобы всё прогрузилось
    setTimeout(start, 1000);
</script>

</body>
</html>


