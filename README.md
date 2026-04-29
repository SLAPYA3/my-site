<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SLAPYA_TV</title>
    <style>
        :root { --main: #ff004d; --bg: #000; --card: #1a1a1a; --text: #fff; }
        body { font-family: sans-serif; background: var(--bg); color: var(--text); margin: 0; padding-top: 60px; }
        header { background: #111; height: 50px; position: fixed; top: 0; width: 100%; border-bottom: 2px solid var(--main); display: flex; justify-content: center; align-items: center; }
        .logo { font-size: 22px; font-weight: bold; text-transform: uppercase; }
        .logo span { color: var(--main); }
        .container { width: 95%; max-width: 600px; margin: 0 auto; }
        .card { background: var(--card); border-radius: 12px; padding: 20px; border: 1px solid #333; margin-top: 20px; }
        #chatWindow { height: 350px; overflow-y: auto; background: #080808; border-radius: 8px; padding: 15px; display: flex; flex-direction: column; gap: 10px; margin-bottom: 15px; }
        .msg { padding: 10px; border-radius: 10px; background: #333; font-size: 14px; max-width: 80%; }
        .msg.my { align-self: flex-end; background: var(--main); }
        input { width: 100%; background: #222; border: 1px solid #444; color: #fff; padding: 12px; border-radius: 8px; box-sizing: border-box; outline: none; }
        .btn { background: var(--main); color: #fff; border: none; padding: 12px; border-radius: 8px; cursor: pointer; font-weight: bold; width: 100%; margin-top: 10px; }
    </style>
</head>
<body>

<header><div class="logo">SLAPYA<span>_TV</span></div></header>

<div class="container">
    <div class="card">
        <div id="stat" style="text-align:center; font-size:12px; color:yellow; margin-bottom:10px;">ПОДКЛЮЧЕНИЕ...</div>
        <div id="chatWindow"></div>
        <input type="text" id="chatInput" placeholder="Напиши что-нибудь...">
        <button class="btn" id="sendBtn">ОТПРАВИТЬ 📡</button>
    </div>
</div>

<script type="module">
    // Импортируем Firebase по-новому
    import { initializeApp } from "https://gstatic.com";
    import { getDatabase, ref, push, onChildAdded, onValue } from "https://gstatic.com";

    const firebaseConfig = {
        apiKey: "AIzaSyBCUf9EeU4Imh8kzHto2rNor-P_bgjpeWU",
        authDomain: "://firebaseapp.com",
        databaseURL: "https://slapya-tv-default-rtdb.firebaseio.com",
        projectId: "slapya-tv"
    };

    // Инициализация
    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);
    const chatRef = ref(db, 'chat');

    // Проверка связи
    const connectedRef = ref(db, ".info/connected");
    onValue(connectedRef, (snap) => {
        const stat = document.getElementById("stat");
        if (snap.val() === true) {
            stat.innerText = "СВЯЗЬ: ЕСТЬ! ✅";
            stat.style.color = "lime";
        } else {
            stat.innerText = "СВЯЗЬ: НЕТ ❌ (Обнови страницу)";
            stat.style.color = "red";
        }
    });

    // Отправка
    document.getElementById("sendBtn").onclick = () => {
        const input = document.getElementById("chatInput");
        if (!input.value.trim()) return;
        push(chatRef, {
            message: input.value,
            time: Date.now()
        });
        input.value = "";
    };

    // Получение
    onChildAdded(chatRef, (data) => {
        const msg = data.val();
        const win = document.getElementById("chatWindow");
        const div = document.createElement("div");
        div.className = "msg my"; // Для теста все сообщения справа
        div.innerText = msg.message;
        win.appendChild(div);
        win.scrollTop = win.scrollHeight;
    });
</script>

</body>
</html>
