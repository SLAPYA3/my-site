<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>SLAPYA_TV | LIVE</title>
    <!-- Самый надежный способ подключения библиотек -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        body { background: #000; color: #fff; font-family: sans-serif; text-align: center; margin: 0; padding-top: 50px; }
        .card { background: #111; border: 2px solid #ff004d; border-radius: 15px; width: 90%; max-width: 500px; margin: 0 auto; padding: 20px; box-shadow: 0 0 20px rgba(255, 0, 77, 0.3); }
        #status { font-size: 14px; margin-bottom: 15px; font-weight: bold; }
        #chatWindow { height: 300px; overflow-y: auto; background: #050505; border-radius: 10px; padding: 10px; margin-bottom: 15px; text-align: left; border: 1px solid #222; }
        .msg { background: #ff004d; padding: 8px 12px; border-radius: 10px; margin-bottom: 8px; display: inline-block; clear: both; float: right; font-size: 14px; }
        input { width: 100%; padding: 12px; border-radius: 8px; border: none; background: #222; color: #fff; box-sizing: border-box; outline: none; border: 1px solid #333; }
        button { width: 100%; padding: 12px; background: #ff004d; color: #fff; border: none; border-radius: 8px; margin-top: 10px; font-weight: bold; cursor: pointer; text-transform: uppercase; }
        button:active { transform: scale(0.98); }
    </style>
</head>
<body>

    <div class="card">
        <div style="font-size: 24px; font-weight: bold; margin-bottom: 10px;">SLAPYA<span>_TV</span></div>
        <div id="status" style="color: #ffcc00;">СИГНАЛ: ПОИСК...</div>
        
        <div id="chatWindow">
            <div style="color: #444; font-size: 12px; text-align: center;">Добро пожаловать в эфир</div>
        </div>
        
        <input type="text" id="chatInput" placeholder="Введите сообщение...">
        <button onclick="sendMsg()">ОТПРАВИТЬ В ЭФИР</button>
    </div>

<script>
    // ТВОИ ДАННЫЕ (ПРОВЕРЕНЫ)
    const firebaseConfig = {
        apiKey: "AIzaSyBCUf9EeU4Imh8kzHto2rNor-P_bgjpeWU",
        authDomain: "://firebaseapp.com",
        databaseURL: "https://slapya-tv-default-rtdb.firebaseio.com",
        projectId: "slapya-tv"
    };

    // Запуск Firebase
    try {
        firebase.initializeApp(firebaseConfig);
        const database = firebase.database();

        // Проверка соединения с базой
        database.ref(".info/connected").on("value", function(snap) {
            const stat = document.getElementById("status");
            if (snap.val() === true) {
                stat.innerText = "СИГНАЛ: ЕСТЬ! ✅";
                stat.style.color = "#00ff00";
            } else {
                stat.innerText = "СИГНАЛ: ПОТЕРЯН ❌";
                stat.style.color = "#ff0000";
            }
        });

        // Функция отправки
        function sendMsg() {
            const input = document.getElementById('chatInput');
            const text = input.value.trim();
            if (!text) return;

            database.ref('global_chat').push({
                message: text,
                timestamp: Date.now()
            }).then(function() {
                input.value = "";
            }).catch(function(error) {
                alert("ОШИБКА БАЗЫ: " + error.message);
            });
        }

        // Получение сообщений
        database.ref('global_chat').limitToLast(20).on('child_added', function(snapshot) {
            const data = snapshot.val();
            const win = document.getElementById('chatWindow');
            const div = document.createElement('div');
            div.className = 'msg';
            div.innerText = data.message;
            win.appendChild(div);
            // Листаем вниз автоматически
            win.scrollTop = win.scrollHeight;
        });

    } catch (e) {
        document.getElementById("status").innerText = "ОШИБКА ЗАПУСКА!";
        console.error(e);
    }
</script>

</body>
</html>

