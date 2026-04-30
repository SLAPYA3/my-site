<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Чат для Вас!</title>
    <style>
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { background: #080808; color: #fff; font-family: sans-serif; margin: 0; padding: 0; overflow: hidden; height: 100vh; display: flex; align-items: center; justify-content: center; position: relative; }
        
        .top-yt-link { position: absolute; top: 20px; right: 20px; text-decoration: none; z-index: 100; transition: 0.3s; }
        .top-yt-link:hover { transform: scale(1.1); filter: drop-shadow(0 0 15px #ff004d); }
        .yt-img { width: 60px; height: 60px; border-radius: 50%; border: 2px solid #ff004d; object-fit: cover; background: #222; }

        .card { background: #111; border: 1px solid #222; border-radius: 25px; width: 95%; max-width: 400px; height: 85vh; display: flex; flex-direction: column; padding: 15px; box-shadow: 0 20px 60px rgba(0,0,0,1); position: relative; }
        .header { display: flex; justify-content: space-between; align-items: center; padding-bottom: 12px; border-bottom: 1px solid #1a1a1a; margin-bottom: 10px; }
        .logo { font-size: 18px; font-weight: 900; color: #ff004d; text-transform: uppercase; }
        
        #chatBox { flex: 1; overflow-y: auto; background: #050505; border-radius: 18px; padding: 12px; display: flex; flex-direction: column; gap: 10px; }
        .msg-wrapper { display: flex; align-items: flex-end; gap: 8px; max-width: 85%; animation: fadeIn 0.2s ease; }
        .msg-wrapper.mine { flex-direction: row-reverse; align-self: flex-end; }
        .avatar, .current-user-av { width: 32px; height: 32px; border-radius: 50%; border: 1px solid #ff004d; object-fit: cover; background: #222; flex-shrink: 0; }
        
        .msg { padding: 8px 12px; border-radius: 15px; font-size: 14px; line-height: 1.4; }
        .mine .msg { background: #ff004d; color: white; border-bottom-right-radius: 2px; }
        .others .msg { background: #1a1a1a; color: #eee; border-bottom-left-radius: 2px; border: 1px solid #222; }
        
        .input-area { display: flex; gap: 8px; align-items: center; margin-top: 15px; background: #1a1a1a; padding: 6px 12px; border-radius: 20px; }
        input { flex: 1; padding: 10px; border: none; background: transparent; color: #fff; outline: none; }
        .file-label { cursor: pointer; font-size: 18px; opacity: 0.7; }
        #avatarInput { display: none; }
        .send-btn { background: #ff004d; color: #fff; border: none; border-radius: 50%; width: 35px; height: 35px; cursor: pointer; font-weight: bold; }
    </style>
</head>
<body>

    <a href="https://youtube.com" target="_blank" class="top-yt-link">
        <img src="yt.jpg" onerror="this.src='https://pravatar.cc'" class="yt-img">
    </a>

    <div class="card">
        <div class="header">
            <div class="logo">ЧАТ ДЛЯ ВАС!</div>
            <!-- Кнопка смены аватарки -->
            <label class="file-label" title="Сменить аву">📸 <input type="file" id="avatarInput" accept="image/*"></label>
        </div>
        <div id="chatBox"></div>
        <div class="input-area">
            <!-- Аватарка в поле ввода, которая не пропадает -->
            <img id="myCurrentAv" src="" class="current-user-av">
            <input type="text" id="chatInput" placeholder="Напишите..." autocomplete="off">
            <button class="send-btn" onclick="send()">➤</button>
        </div>
    </div>

<script type="module">
    import { initializeApp } from "https://gstatic.com";
    import { getDatabase, ref, push, onChildAdded, serverTimestamp } from "https://gstatic.com";

    const firebaseConfig = { databaseURL: "https://firebaseio.com" };
    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);
    const messagesRef = ref(db, 'messages');

    // 1. Ищем старый ID или создаем новый
    const userId = localStorage.getItem('userId') || Math.random().toString(36).substring(7);
    localStorage.setItem('userId', userId);
    
    // 2. Ищем сохраненную аватарку или ставим стандартную
    let userAvatar = localStorage.getItem('userAvatar') || 'https://pravatar.cc';
    const myImg = document.getElementById('myCurrentAv');
    myImg.src = userAvatar;

    // Смена аватарки через галерею
    document.getElementById('avatarInput').addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = function(event) {
            userAvatar = event.target.result;
            // СОХРАНЯЕМ В ПАМЯТЬ БРАУЗЕРА
            localStorage.setItem('userAvatar', userAvatar);
            myImg.src = userAvatar;
        };
        reader.readAsDataURL(file);
    });

    window.send = function() {
        const input = document.getElementById('chatInput');
        if (!input.value.trim()) return;
        push(messagesRef, { userId, avatar: userAvatar, text: input.value, timestamp: serverTimestamp() });
        input.value = "";
    };

    onChildAdded(messagesRef, (data) => {
        const msg = data.val();
        const isMine = msg.userId === userId;
        const wrapper = document.createElement('div');
        wrapper.className = `msg-wrapper ${isMine ? 'mine' : 'others'}`;
        wrapper.innerHTML = `<img src="${msg.avatar}" class="avatar"><div class="msg">${msg.text}</div>`;
        document.getElementById('chatBox').appendChild(wrapper);
        document.getElementById('chatBox').scrollTop = document.getElementById('chatBox').scrollHeight;
    });
</script>
</body>
</html>










