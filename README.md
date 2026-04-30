<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Чат для Вас!</title>
    <style>
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { background: #080808; color: #fff; font-family: -apple-system, system-ui, sans-serif; margin: 0; padding: 10px; overflow: hidden; height: 100vh; display: flex; align-items: center; justify-content: center; }
        
        .card { background: #111; border: 1px solid #222; border-radius: 20px; width: 100%; max-width: 450px; height: 90vh; display: flex; flex-direction: column; padding: 15px; box-shadow: 0 15px 50px rgba(0,0,0,0.9); position: relative; }
        
        .header { display: flex; justify-content: space-between; align-items: center; padding-bottom: 15px; border-bottom: 1px solid #1a1a1a; margin-bottom: 10px; }
        .logo { font-size: 18px; font-weight: 800; color: #ff004d; letter-spacing: 0.5px; }
        
        .nav-icons { display: flex; gap: 12px; align-items: center; }
        
        /* Кнопка-аватарка с твоей картинкой */
        .yt-link { text-decoration: none; transition: 0.3s; display: flex; align-items: center; justify-content: center; position: relative; }
        .yt-link:hover { transform: scale(1.15); }
        .yt-img { 
            width: 40px; height: 40px; border-radius: 50%; border: 2px solid #ff004d; object-fit: cover; 
            box-shadow: 0 0 15px rgba(255, 0, 77, 0.3); 
        }
        .yt-status { width: 10px; height: 10px; background: #00ff00; border: 2px solid #111; border-radius: 50%; position: absolute; bottom: 0; right: 0; }
        
        #chatBox { flex: 1; overflow-y: auto; background: #050505; border-radius: 15px; padding: 12px; display: flex; flex-direction: column; gap: 10px; scroll-behavior: smooth; }
        #chatBox::-webkit-scrollbar { width: 4px; }
        #chatBox::-webkit-scrollbar-thumb { background: #222; border-radius: 10px; }

        .msg-wrapper { display: flex; align-items: flex-end; gap: 8px; max-width: 85%; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        .msg-wrapper.mine { flex-direction: row-reverse; align-self: flex-end; }
        .msg-wrapper.others { align-self: flex-start; }

        .avatar { width: 32px; height: 32px; border-radius: 50%; background: #222; object-fit: cover; border: 1.5px solid #ff004d; flex-shrink: 0; }
        
        .msg { padding: 10px 14px; border-radius: 18px; font-size: 14px; line-height: 1.4; position: relative; }
        .mine .msg { background: #ff004d; color: white; border-bottom-right-radius: 4px; }
        .others .msg { background: #1a1a1a; color: #eee; border-bottom-left-radius: 4px; border: 1px solid #222; }
        
        .msg-info { font-size: 9px; opacity: 0.5; margin-bottom: 3px; display: block; text-transform: uppercase; letter-spacing: 0.5px; }
        
        .input-area { display: flex; gap: 8px; align-items: center; margin-top: 15px; background: #151515; padding: 8px; border-radius: 12px; }
        input { flex: 1; padding: 10px; border: none; background: transparent; color: #fff; outline: none; font-size: 14px; }
        
        .file-label { cursor: pointer; font-size: 20px; transition: 0.2s; opacity: 0.7; }
        .file-label:hover { opacity: 1; color: #ff004d; }
        #avatarInput { display: none; }
        
        .send-btn { background: #ff004d; color: #fff; border: none; border-radius: 10px; width: 40px; height: 40px; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 18px; flex-shrink: 0; }
    </style>
</head>
<body>

    <div class="card">
        <div class="header">
            <div class="logo">ЧАТ ДЛЯ ВАС!</div>
            <div class="nav-icons">
                <a href="https://youtube.com" target="_blank" class="yt-link" title="Мой канал">
                    <!-- Твоя картинка закодирована в Base64 -->
                    <img src="https://ibb.co" onerror="this.src='https://pravatar.cc'" class="yt-img">
                    <div class="yt-status"></div>
                </a>
                <label class="file-label" title="Изменить свою аватарку">
                    📸 <input type="file" id="avatarInput" accept="image/*">
                </label>
            </div>
        </div>
        
        <div id="chatBox"></div>
        
        <div class="input-area">
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

    const userId = localStorage.getItem('userId') || Math.random().toString(36).substring(7);
    localStorage.setItem('userId', userId);

    let userAvatar = localStorage.getItem('userAvatar') || 'https://pravatar.cc';

    document.getElementById('chatInput').addEventListener('keypress', (e) => { if(e.key === 'Enter') send(); });

    document.getElementById('avatarInput').addEventListener('change', function(e) {
        const file = e.target.files;
        if (!file) return;
        const reader = new FileReader();
        reader.onload = function(event) {
            const img = new Image();
            img.onload = function() {
                const canvas = document.createElement('canvas');
                const ctx = canvas.getContext('2d');
                canvas.width = 80; canvas.height = 80;
                ctx.drawImage(img, 0, 0, 80, 80);
                userAvatar = canvas.toDataURL('image/jpeg', 0.6);
                localStorage.setItem('userAvatar', userAvatar);
            };
            img.src = event.target.result;
        };
        reader.readAsDataURL(file);
    });

    window.send = function() {
        const input = document.getElementById('chatInput');
        const text = input.value.trim();
        if (!text) return;
        push(messagesRef, { userId, avatar: userAvatar, text: text, timestamp: serverTimestamp() });
        input.value = "";
    };

    onChildAdded(messagesRef, (data) => {
        const msg = data.val();
        const isMine = msg.userId === userId;
        const time = msg.timestamp ? new Date(msg.timestamp).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) : '';
        
        const wrapper = document.createElement('div');
        wrapper.className = `msg-wrapper ${isMine ? 'mine' : 'others'}`;
        wrapper.innerHTML = `
            <img src="${msg.avatar || 'https://pravatar.cc'}" class="avatar">
            <div class="msg">
                <span class="msg-info">${isMine ? 'Вы' : 'Аноним'} ${time}</span>
                ${msg.text}
            </div>
        `;
        const box = document.getElementById('chatBox');
        box.appendChild(wrapper);
        box.scrollTop = box.scrollHeight;
    });
</script>
</body>
</html>





