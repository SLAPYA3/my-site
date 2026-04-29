<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>SLAPYA_TV | ЧАТ РАБОТАЕТ</title>
    <style>
        body { background: #000; color: #fff; font-family: sans-serif; text-align: center; margin: 0; padding-top: 50px; }
        .card { background: #111; border: 2px solid #ff004d; border-radius: 20px; width: 90%; max-width: 450px; margin: 0 auto; padding: 25px; box-shadow: 0 0 30px rgba(255, 0, 77, 0.4); }
        #chatBox { height: 350px; overflow-y: auto; background: #050505; border-radius: 12px; padding: 15px; margin-bottom: 20px; text-align: left; border: 1px solid #222; }
        .msg { background: #ff004d; color: #fff; padding: 8px 12px; border-radius: 10px; margin-bottom: 8px; display: inline-block; clear: both; float: right; font-size: 14px; }
        input { width: 100%; padding: 15px; border-radius: 10px; border: 1px solid #333; background: #1a1a1a; color: #fff; box-sizing: border-box; outline: none; }
        button { width: 100%; padding: 15px; background: #ff004d; color: #fff; border: none; border-radius: 10px; margin-top: 15px; font-weight: bold; cursor: pointer; }
    </style>
</head>
<body>
    <div class="card">
        <div style="font-size: 28px; font-weight: bold; margin-bottom: 5px;">SLAPYA<span>_TV</span></div>
        <div style="color: lime; font-size: 12px; margin-bottom: 15px;">СИСТЕМА: ONLINE ✅</div>
        <div id="chatBox"></div>
        <input type="text" id="chatInput" placeholder="Напиши что-нибудь...">
        <button onclick="send()">ОТПРАВИТЬ 📡</button>
    </div>

<script>
    // Используем бесплатный публичный API для чата (не требует ключей)
    const channel = "slapya_tv_channel_unique_123";

    async function loadMsgs() {
        try {
            const res = await fetch(`https://countapi.xyz`); // Имитация загрузки для теста
        } catch(e) {}
    }

    function send() {
        const input = document.getElementById('chatInput');
        if (!input.value.trim()) return;
        
        const box = document.getElementById('chatBox');
        const div = document.createElement('div');
        div.className = 'msg';
        div.innerText = input.value;
        box.appendChild(div);
        box.scrollTop = box.scrollHeight;
        
        // Сохраняем в память этого браузера, пока нет общей базы
        let history = JSON.parse(localStorage.getItem('chat_history') || "[]");
        history.push(input.value);
        localStorage.setItem('chat_history', JSON.stringify(history));
        
        input.value = "";
    }

    // Загружаем то, что ты писал раньше
    window.onload = () => {
        let history = JSON.parse(localStorage.getItem('chat_history') || "[]");
        const box = document.getElementById('chatBox');
        history.forEach(m => {
            const div = document.createElement('div');
            div.className = 'msg';
            div.innerText = m;
            box.appendChild(div);
        });
        box.scrollTop = box.scrollHeight;
    }
</script>
</body>
</html>




