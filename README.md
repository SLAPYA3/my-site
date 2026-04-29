<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>SLAPYA_TV | UNSTOPPABLE</title>
    <!-- Подключаем через супер-надежное зеркало Cloudflare -->
    <script src="https://cloudflare.com"></script>
    <script src="https://cloudflare.com"></script>
    <style>
        body { background: #000; color: #fff; font-family: 'Segoe UI', sans-serif; text-align: center; margin: 0; padding-top: 50px; }
        .card { background: #111; border: 2px solid #ff004d; border-radius: 20px; width: 90%; max-width: 450px; margin: 0 auto; padding: 25px; box-shadow: 0 0 30px rgba(255, 0, 77, 0.4); }
        #status { font-size: 14px; margin-bottom: 20px; letter-spacing: 1px; }
        #chatBox { height: 350px; overflow-y: auto; background: #050505; border-radius: 12px; padding: 15px; margin-bottom: 20px; text-align: left; border: 1px solid #222; }
        .msg { background: #ff004d; color: #fff; padding: 10px 15px; border-radius: 15px; margin-bottom: 10px; display: inline-block; clear: both; float: right; font-size: 14px; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        input { width: 100%; padding: 15px; border-radius: 10px; border: 1px solid #333; background: #1a1a1a; color: #fff; box-sizing: border-box; outline: none; font-size: 16px; }
        button { width: 100%; padding: 15px; background: #ff004d; color: #fff; border: none; border-radius: 10px; margin-top: 15px; font-weight: bold; cursor: pointer; text-transform: uppercase; letter-spacing: 1px; }
        button:hover { background: #ff2e6e; }
    </style>
</head>
<body>

    <div class="card">
        <div style="font-size: 28px; font-weight: bold; margin-bottom: 5px;">SLAPYA<span>_TV</span></div>
        <div id="status" style="color: #ffcc00;">ИНИЦИАЛИЗАЦИЯ...</div>
        
        <div id="chatBox" id="chat"></div>
        
        <input type="text" id="chatInput" placeholder="Напиши сообщение...">
        <button onclick="send()">ОТПРАВИТЬ В ЭФИР 📡</button>
    </div>

<script>
    const config = {
        apiKey: "AIzaSyBCUf9EeU4Imh8kzHto2rNor-P_bgjpeWU",
        authDomain: "://firebaseapp.com",
        databaseURL: "https://firebaseio.com",
        projectId: "slapya-tv"
    };

    function init() {
        if (typeof firebase === 'undefined') {
            document.getElementById("status").innerText = "СЕТЬ ВСЁ ЕЩЁ БЛОКИРУЕТ ❌";
            document.getElementById("status").style.color = "red";
            return;
        }

        firebase.initializeApp(config);
        const db = firebase.database();

        // Проверка связи
        db.ref(".info/connected").on("value", (snap) => {
            const s = document.getElementById("status");
            if (snap.val() === true) {
                s.innerText = "СИГНАЛ: ЕСТЬ! ✅";
                s.style.color = "#00ff00";
            } else {
                s.innerText = "СИГНАЛ: ПОИСК... ⏳";
                s.style.color = "#ffcc00";
            }
        });

        // Отправка
        window.send = function() {
            const input = document.getElementById('chatInput');
            if (!input.value.trim()) return;
            db.ref('global_chat').push({ m: input.value }).then(() => {
                input.value = "";
            }).catch(e => alert("Ошибка базы: " + e.message));
        };

        // Чтение
        db.ref('global_chat').limitToLast(15).on('child_added', (snap) => {
            const box = document.getElementById('chatBox');
            const div = document.createElement('div');
            div.className = 'msg';
            div.innerText = snap.val().m;
            box.appendChild(div);
            box.scrollTop = box.scrollHeight;
        });
    }

    // Запуск
    setTimeout(init, 1000);
</script>

</body>
</html>



