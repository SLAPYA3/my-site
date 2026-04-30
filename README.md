<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Чат для Вас!</title>
    <style>
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { background: #080808; color: #fff; font-family: sans-serif; margin: 0; padding: 0; overflow: hidden; height: 100vh; display: flex; align-items: center; justify-content: center; position: relative; }
        
        /* КНОПКА ЮТУБА В УГЛУ */
        .top-yt-link { 
            position: fixed; 
            top: 20px; 
            right: 20px; 
            text-decoration: none; 
            z-index: 9999; 
            transition: 0.3s;
            display: block;
        }
        .top-yt-link:hover { transform: scale(1.1); filter: drop-shadow(0 0 15px #ff004d); }
        .yt-img { 
            width: 65px; height: 65px; 
            border-radius: 50%; 
            border: 3px solid #ff004d; 
            object-fit: cover; 
            background: #222;
            display: block;
        }
        .yt-status { 
            width: 14px; height: 14px; 
            background: #00ff00; 
            border: 2px solid #080808; 
            border-radius: 50%; 
            position: absolute; 
            bottom: 4px; 
            right: 4px; 
            box-shadow: 0 0 8px #00ff00;
        }

        .card { background: #111; border: 1px solid #222; border-radius: 25px; width: 95%; max-width: 400px; height: 85vh; display: flex; flex-direction: column; padding: 15px; box-shadow: 0 20px 60px rgba(0,0,0,1); position: relative; z-index: 10; }
        .header { display: flex; justify-content: space-between; align-items: center; padding-bottom: 12px; border-bottom: 1px solid #1a1a1a; margin-bottom: 10px; }
        .logo { font-size: 18px; font-weight: 900; color: #ff004d; text-transform: uppercase; letter-spacing: 1px; }
        
        #chatBox { flex: 1; overflow-y: auto; background: #050505; border-radius: 18px; padding: 12px; display: flex; flex-direction: column; gap: 10px; }
        #chatBox::-webkit-scrollbar { width: 4px; }
        #chatBox::-webkit-scrollbar-thumb { background: #333; border-radius: 10px; }

        .msg-wrapper { display: flex; align-items: flex-end; gap: 8px; max-width: 85%; animation: fadeIn 0.25s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        .msg-wrapper.mine { flex-direction: row-reverse; align-self: flex-end; }
        .msg-wrapper.others { align-self: flex-start; }

        .avatar { width: 32px; height: 32px; border-radius: 50%; border: 1px solid #ff004d; object-fit: cover; background: #222; }
        .msg { padding: 10px 14px; border-radius: 18px; font-size: 14px; line-height: 1.4; }
        .mine .msg { background: #ff004d; color: white; border-bottom-right-radius: 2px; }
        .others .msg { background: #1a1a1a; color: #eee; border-bottom-left-radius: 2px; border: 1px solid #222; }
        
        .msg-info { font-size: 9px; opacity: 0.5; margin-bottom: 2px; display: block; }
        .input-area { display: flex; gap: 8px; align-items: center; margin-top: 15px; background: #1a1a1a; padding: 6px 12px; border-radius: 15px; }
        input { flex: 1; padding: 10px; border: none; background: transparent; color: #fff; outline: none; font-size: 15px; }
        .file-btn { cursor: pointer; font-size: 20px; opacity: 0.7; }
        #avatarInput { display: none; }
        .send-btn { background: #ff004d; color: #fff; border: none; border-radius: 50%; width: 38px; height: 38px; cursor: pointer; display: flex; align-items: center; justify-content: center; }
    </style>
</head>
<body>

    <a href="https://youtube.com" target="_blank" class="top-yt-link">
        <!-- Вставил картинку через надежный CDN -->
        <img src="https://imgbox.com" class="yt-img" id="ytIcon">
        <div class="yt-status"></div>
    </a>

    <div class="card">
        <div class="header">
            <div class="logo">ЧАТ ДЛЯ ВАС!</div>
            <label class="file-btn">📸 <input type="file" id="avatarInput" accept="image/*"></label>
        </div>
        <div id="chatBox"></div>
        <div class="input-area">
            <input type="text" id="chatInput" placeholder="Напишите сообщение..." autocomplete="off">
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

    const userId = localStorage.getItem('userId') || Math.random().toString(36).substring(7);
    localStorage.setItem('userId', userId);
    let userAvatar = localStorage.getItem('userAvatar') || 'https://pravatar.cc';

    // Фоллбек для картинки, если ссылка умрет
    const ytIcon = document.getElementById('ytIcon');
    ytIcon.onerror = function() {
        this.src = "https://youtube.com";
    };

    document.getElementById('chatInput').addEventListener('keypress', (e) => { if(e.key === 'Enter') send(); });

    document.getElementById('avatarInput').addEventListener('change', function(e) {
        const file = e.target.files;
        if (!file) return;
        const reader = new FileReader();
        reader.onload = function(event) {
            userAvatar = event.target.result;
            localStorage.setItem('userAvatar', userAvatar);
            alert("Аватарка обновлена!");
        };
        reader.readAsDataURL(file);
    });

    window.send = function() {
        const input = document.getElementById('chatInput');
        const text = input.value.trim();
        if (!text) return;
        push(messagesRef, { userId, avatar: userAvatar, text, timestamp: serverTimestamp() });
        input.value = "";
    };

    onChildAdded(messagesRef, (data) => {
        const msg = data.val();
        const isMine = msg.userId === userId;
        const time = msg.timestamp ? new Date(msg.timestamp).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) : '';
        const wrapper = document.createElement('div');
        wrapper.className = `msg-wrapper ${isMine ? 'mine' : 'others'}`;
        wrapper.innerHTML = `<img src="${msg.avatar}" class="avatar"><div class="msg"><span class="msg-info">${isMine ? 'Вы' : 'Аноним'} ${time}</span>${msg.text}</div>`;
        const box = document.getElementById('chatBox');
        box.appendChild(wrapper);
        box.scrollTop = box.scrollHeight;
    });
</script>
</body>
</html>




