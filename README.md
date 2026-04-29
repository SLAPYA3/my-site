<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>SLAPYA_TV | TEST</title>
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        body { background: #000; color: #fff; font-family: sans-serif; text-align: center; }
        #chatWindow { height: 300px; overflow-y: auto; background: #111; margin: 20px auto; width: 90%; max-width: 500px; border: 1px solid #ff004d; padding: 10px; text-align: left; }
        input { padding: 10px; width: 70%; border-radius: 5px; border: none; }
        button { padding: 10px 20px; background: #ff004d; color: #fff; border: none; border-radius: 5px; cursor: pointer; }
        .msg { margin-bottom: 8px; border-bottom: 1px solid #222; padding-bottom: 4px; }
    </style>
</head>
<body>

    <h1>SLAPYA_TV TEST CHAT</h1>
    <div id="chatWindow"></div>
    <input type="text" id="chatInput" placeholder="Напиши привет...">
    <button onclick="sendMsg()">ОТПРАВИТЬ</button>

<script>
    const firebaseConfig = {
        apiKey: "AIzaSyBCUf9EeU4Imh8kzHto2rNor-P_bgjpeWU",
        authDomain: "://firebaseapp.com",
        databaseURL: "https://firebaseio.com",
        projectId: "slapya-tv",
        storageBucket: "://appspot.com",
        messagingSenderId: "136766849091",
        appId: "1:136766849091:web:deba1aa85e51a6e4894f6f"
    };

    // Инициализация
    firebase.initializeApp(firebaseConfig);
    const database = firebase.database();

    function sendMsg() {
        const input = document.getElementById('chatInput');
        const text = input.value;
        if (!text) return;

        database.ref('chat').push({
            message: text
        }).then(() => {
            input.value = "";
        }).catch((err) => {
            alert("ОШИБКА: " + err.message);
        });
    }

    database.ref('chat').on('child_added', (snapshot) => {
        const msg = snapshot.val();
        const win = document.getElementById('chatWindow');
        const div = document.createElement('div');
        div.className = 'msg';
        div.innerText = msg.message;
        win.appendChild(div);
        win.scrollTop = win.scrollHeight;
    });
</script>
</body>
</html>
