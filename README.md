<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SLAPYA_TV</title>
    <!-- Firebase SDK (Версия 8 - самая стабильная для нас) -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
    <style>
        :root { --main: #ff004d; --bg: #000; --card: #1a1a1a; --text: #fff; }
        body { font-family: 'Segoe UI', Roboto, sans-serif; background: var(--bg); color: var(--text); margin: 0; padding-top: 60px; }
        header { background: #111; height: 50px; position: fixed; top: 0; width: 100%; z-index: 1000; border-bottom: 2px solid var(--main); display: flex; justify-content: center; align-items: center; }
        .logo { font-size: 22px; font-weight: bold; text-transform: uppercase; letter-spacing: 2px; }
        .logo span { color: var(--main); }
        .container { width: 900px; margin: 0 auto; display: flex; gap: 20px; }
        @media (max-width: 920px) { .container { width: 95%; flex-direction: column; } nav { width: 100% !important; } main { width: 100% !important; } }
        nav { width: 180px; }
        nav li { list-style: none; padding: 12px; color: #aaa; cursor: pointer; border-radius: 8px; margin-bottom: 5px; font-size: 14px; }
        nav li:hover, nav li.active { background: var(--card); color: var(--main); font-weight: bold; }
        main { flex-grow: 1; display: none; }
        main.active { display: block; }
        .card { background: var(--card); border-radius: 12px; padding: 20px; margin-bottom: 20px; border: 1px solid #333; }
        #userAva { width: 150px; height: 150px; object-fit: cover; border-radius: 12px; background: #333; border: 2px solid #444; }
        .file-label { background: #333; color: #fff; padding: 10px 15px; display: inline-block; border-radius: 6px; cursor: pointer; margin-top: 10px; font-size: 12px; border: 1px solid #444; font-weight: bold; }
        #chatWindow { height: 400px; overflow-y: auto; background: #080808; border-radius: 8px; padding: 15px; display: flex; flex-direction: column; gap: 12px; border: 1px solid #222; }
        .msg { padding: 10px 14px; border-radius: 14px; max-width: 75%; font-size: 14px; color: #fff; line-height: 1.4; }
        .msg b { display: block; font-size: 11px; margin-bottom: 4px; opacity: 0.7; }
        .msg.other { align-self: flex-start; background: #333; border-bottom-left-radius: 2px; }
        .msg.my { align-self: flex-end; background: var(--main); border-bottom-right-radius: 2px; }
        input, textarea { width: 100%; background: #222; border: 1px solid #444; color: #fff; padding: 12px; border-radius: 8px; box-sizing: border-box; margin-bottom: 10px; outline: none; }
        .btn { background: var(--main); color: #fff; border: none; padding: 12px 25px; border-radius: 8px; cursor: pointer; font-weight: bold; width: 100%; }
        #status-line { font-size: 11px; text-align: center; margin-bottom: 10px; }
    </style>
</head>
<body>

<header><div class="logo" onclick="location.reload()">SLAPYA<span>_TV</span></div></header>

<div class="container">
    <nav>
        <li id="l_profile" class="active" onclick="showTab('profile')">📺 МОЙ КАНАЛ</li>
        <li id="l_chat" onclick="showTab('chat')">💬 ОБЩИЙ ЧАТ</li>
    </nav>

    <main id="tab_profile" class="active">
        <div class="card" style="display:flex; gap:20px; flex-wrap: wrap;">
            <div style="text-align:center">
                <img id="userAva" src="https://placeholder.com">
                <label class="file-label" for="avaIn">📸 СМЕНИТЬ ФОТО</label>
                <input type="file" id="avaIn" accept="image/*" onchange="changeAvatar()">
            </div>
            <div style="flex:1; min-width: 250px;">
                <h2 id="userName" style="margin:0">SLAPYA USER</h2>
                <input type="text" id="inName" placeholder="Твое имя" style="margin-top:15px">
                <button class="btn" onclick="saveName()">СОХРАНИТЬ</button>
            </div>
        </div>
        <div id="wall"></div>
    </main>

    <main id="tab_chat">
        <div class="card">
            <div id="status-line">СИГНАЛ: <span id="db_stat" style="color:yellow">ПОИСК...</span></div>
            <div id="chatWindow"></div>
            <div style="display:flex; gap:10px; margin-top:15px">
                <input type="text" id="chatInput" placeholder="Напиши что-нибудь..." onkeypress="if(event.keyCode==13) sendMsg()">
                <button class="btn" style="width:auto" onclick="sendMsg()">📡</button>
            </div>
        </div>
    </main>
</div>

<script>
    // ИСПРАВЛЕННЫЙ КОНФИГ
    const firebaseConfig = {
        apiKey: "AIzaSyBCUf9EeU4Imh8kzHto2rNor-P_bgjpeWU",
        authDomain: "://firebaseapp.com",
        databaseURL: "https://slapya-tv-default-rtdb.firebaseio.com/",
        projectId: "slapya-tv"
    };

    if (!firebase.apps.length) { firebase.initializeApp(firebaseConfig); }
    const database = firebase.database();

    // Проверка связи
    database.ref(".info/connected").on("value", (snap) => {
        const s = document.getElementById("db_stat");
        if (snap.val() === true) { s.innerText = "ЕСТЬ! ✅"; s.style.color = "lime"; }
        else { s.innerText = "НЕТ ❌"; s.style.color = "red"; }
    });

    let myName = localStorage.getItem('tvName') || 'User';
    document.getElementById('userName').innerText = myName;
    if(localStorage.getItem('tvAva')) document.getElementById('userAva').src = localStorage.getItem('tvAva');

    function showTab(tab) {
        document.querySelectorAll('main').forEach(m => m.classList.remove('active'));
        document.querySelectorAll('nav li').forEach(l => l.classList.remove('active'));
        document.getElementById('tab_' + tab).classList.add('active');
        document.getElementById('l_' + tab).classList.add('active');
    }

    function saveName() {
        let n = document.getElementById('inName').value.trim();
        if(n) { myName = n; localStorage.setItem('tvName', n); document.getElementById('userName').innerText = n; }
    }

    function changeAvatar() {
        const file = document.getElementById('avaIn').files[0];
        const reader = new FileReader();
        reader.onloadend = () => { document.getElementById('userAva').src = reader.result; localStorage.setItem('tvAva', reader.result); }
        if(file) reader.readAsDataURL(file);
    }

    function sendMsg() {
        let input = document.getElementById('chatInput');
        if(!input.value.trim()) return;
        database.ref('chat').push({ name: myName, message: input.value });
        input.value = '';
    }

    database.ref('chat').limitToLast(50).on('child_added', (data) => {
        let msg = data.val();
        let win = document.getElementById('chatWindow');
        let div = document.createElement('div');
        div.className = msg.name === myName ? 'msg my' : 'msg other';
        div.innerHTML = `<b>${msg.name}</b>${msg.message}`;
        win.appendChild(div);
        win.scrollTop = win.scrollHeight;
    });
</script>
</body>
</html>

