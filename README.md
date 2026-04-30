<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Чат для Вас!</title>
    <style>
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { background: #080808; color: #fff; font-family: sans-serif; margin: 0; padding: 0; overflow: hidden; height: 100vh; display: flex; align-items: center; justify-content: center; position: relative; }
        .top-yt-link { position: absolute; top: 15px; right: 15px; z-index: 9999; }
        .yt-img { width: 55px; height: 55px; border-radius: 50%; border: 2px solid #ff004d; object-fit: cover; }
        .card { background: #111; border: 1px solid #222; border-radius: 20px; width: 95%; max-width: 400px; height: 85vh; display: flex; flex-direction: column; padding: 15px; }
        .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; border-bottom: 1px solid #222; padding-bottom: 10px; }
        .logo { font-size: 18px; font-weight: bold; color: #ff004d; }
        #chatBox { flex: 1; overflow-y: auto; background: #050505; border-radius: 15px; padding: 10px; display: flex; flex-direction: column; gap: 8px; }
        .msg-wrapper { display: flex; align-items: flex-end; gap: 8px; max-width: 85%; }
        .msg-wrapper.mine { flex-direction: row-reverse; align-self: flex-end; }
        .avatar, .current-user-av { width: 32px; height: 32px; border-radius: 50%; border: 1px solid #ff004d; object-fit: cover; background: #222; }
        .msg { padding: 8px 12px; border-radius: 15px; font-size: 14px; line-height: 1.4; word-wrap: break-word; }
        .mine .msg { background: #ff004d; color: #fff; border-bottom-right-radius: 2px; }
        .others .msg { background: #222; color: #eee; border-bottom-left-radius: 2px; }
        .input-area { display: flex; gap: 8px; align-items: center; margin-top: 10px; background: #1a1a1a; padding: 8px; border-radius: 20px; }
        input { flex: 1; padding: 5px; border: none; background: transparent; color: #fff; outline: none; }
        .file-label { cursor: pointer; font-size: 20px; }
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
            <label class="file-label">✈️<input type="file" id="avatarInput" accept="image/*"></label>
        </div>
        <div id="chatBox"></div>
        <div class="input-area">
            <img id="myCurrentAv" src="https://pravatar.cc" class="current-user-av">
            <input type="text" id="chatInput" placeholder="Пиши тут...">
            <button id="sendBtn" class="send-btn">➤</button>
        </div>
    </div>

<script type="module">
    import { initializeApp } from "https://gstatic.com";
    import { getDatabase, ref, push, onChildAdded, serverTimestamp } from "https://gstatic.com";

    const firebaseConfig = { databaseURL: "https://firebaseio.com" };
    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);
    const messagesRef = ref(db, 'messages');

    const userId = localStorage.getItem('userId') || Math.random().toString(36).substring(7);
    localStorage.setItem('userId', userId);
    
    let userAvatar = localStorage.getItem('userAvatar') || 'https://pravatar.cc';
    document.getElementById('myCurrentAv').src = userAvatar;

    document.getElementById('avatarInput').onchange = (e) => {
        const file = e.target.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = (ev) => {
            const img = new Image();
            img.src = ev.target.result;
            img.onload = () => {
                const canvas = document.createElement('canvas');
                canvas.width = 64; canvas.height = 64; // Мини-размер
                canvas.getContext('2d').drawImage(img, 0, 0, 64, 64);
                userAvatar = canvas.toDataURL('image/jpeg', 0.4);
                localStorage.setItem('userAvatar', userAvatar);
                document.getElementById('myCurrentAv').src = userAvatar;
            };
        };
        reader.readAsDataURL(file);
    };

    const sendMsg = () => {
        const input = document.getElementById('chatInput');
        const text = input.value.trim();
        if (!text) return;

        push(messagesRef, { userId, avatar: userAvatar, text, time: Date.now() })
        .then(() => { input.value = ""; })
        .catch(err => { alert("Ошибка! Проверь интернет или базу."); });
    };

    document.getElementById('sendBtn').onclick = sendMsg;
    document.getElementById('chatInput').onkeydown = (e) => { if(e.key === 'Enter') sendMsg(); };

    onChildAdded(messagesRef, (data) => {
        const msg = data.val();
        const isMine = msg.userId === userId;
        const div = document.createElement('div');
        div.className = `msg-wrapper ${isMine ? 'mine' : 'others'}`;
        div.innerHTML = `<img src="${msg.avatar}" class="avatar"><div class="msg">${msg.text}</div>`;
        const box = document.getElementById('chatBox');
        box.appendChild(div);
        box.scrollTop = box.scrollHeight;
    });
</script>
</body>
</html>



