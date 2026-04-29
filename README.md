<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>ВКонтакте | Моя Страница</title>
    <style>
        body { font-family: tahoma, arial, verdana, sans-serif, lucida sans; background: #edeef0; margin: 0; padding-top: 42px; color: #000; }
        
        /* Верхняя панель */
        header { background: #4a76a8; height: 42px; position: fixed; top: 0; width: 100%; z-index: 1000; display: flex; justify-content: center; }
        .header-wrap { width: 960px; display: flex; align-items: center; justify-content: space-between; padding: 0 15px; }
        .logo { color: #fff; font-weight: bold; font-size: 19px; text-decoration: none; display: flex; align-items: center; }
        .search { background: #224b7a; border: none; border-radius: 12px; padding: 5px 15px; width: 180px; color: #fff; font-size: 12px; outline: none; }

        .container { width: 960px; margin: 0 auto; display: flex; padding: 15px 0; gap: 20px; }
        
        /* Левая колонка */
        nav { width: 140px; }
        nav ul { list-style: none; padding: 0; margin: 0; }
        nav li { padding: 4px 10px; font-size: 12.5px; color: #2a5885; cursor: pointer; }
        nav li:hover { background: #dae1e8; border-radius: 4px; }
        nav li .count { float: right; color: #939393; }

        /* Центр и профиль */
        main { width: 770px; display: flex; gap: 20px; }
        .col-left { width: 200px; }
        .col-right { width: 550px; }
        
        .card { background: #fff; border-radius: 2px; border-bottom: 1px solid #d7d8db; margin-bottom: 15px; padding: 15px; box-shadow: 0 1px 0 0 #d7d8db; }

        /* Профиль */
        .avatar-box img { width: 200px; border-radius: 2px; }
        .btn-gray { background: #e5ebf1; color: #55677d; text-align: center; padding: 7px; margin-top: 10px; font-size: 12px; cursor: pointer; font-weight: bold; }
        
        .profile-info h2 { margin: 0; font-size: 19px; font-weight: normal; padding-bottom: 10px; border-bottom: 1px solid #dae1e8; }
        .status { color: #656565; font-size: 12px; margin: 8px 0; }
        .info-row { display: flex; font-size: 12.5px; margin-bottom: 6px; }
        .label { width: 160px; color: #828282; }
        .value { color: #2a5885; }

        /* Статистика */
        .stats { display: flex; border-top: 1px solid #dae1e8; margin-top: 15px; padding-top: 15px; text-align: center; }
        .stat-item { flex: 1; text-decoration: none; color: #2a5885; }
        .stat-num { font-size: 19px; }
        .stat-label { font-size: 11px; color: #828282; }

        /* Стена */
        .post-input textarea { width: 100%; border: 1px solid #dae1e8; padding: 8px; box-sizing: border-box; resize: none; outline: none; }
        .post-btn { background: #5181b8; color: #fff; border: none; padding: 6px 16px; border-radius: 4px; font-size: 12px; float: right; margin-top: 10px; cursor: pointer; }
        
        .wall-post { border-top: 1px solid #dae1e8; padding: 15px 0; display: flex; gap: 12px; }
        .mini-ava { width: 50px; height: 50px; background: #eee; border-radius: 50%; }
        .post-author { color: #2a5885; font-weight: bold; font-size: 12.5px; margin-bottom: 4px; }
        .post-txt { font-size: 13px; line-height: 1.5; }
    </style>
</head>
<body>

<header>
    <div class="header-wrap">
        <a href="#" class="logo">вконтакте</a>
        <input type="text" class="search" placeholder="Поиск">
        <div style="color:#fff; font-size:12px;">Выйти</div>
    </div>
</header>

<div class="container">
    <nav>
        <ul>
            <li style="font-weight:bold; background:#dae1e8; border-radius:4px;">Моя Страница</li>
            <li>Новости <span class="count">2</span></li>
            <li>Сообщения</li>
            <li>Друзья</li>
            <li>Сообщества</li>
            <li>Фотографии</li>
            <li>Музыка</li>
            <li>Видео</li>
            <li>Игры</li>
        </ul>
    </nav>

    <main>
        <div class="col-left">
            <div class="card avatar-box" style="padding:0">
                <img src="https://pinimg.com" alt="Дуров">
                <div style="padding:15px">
                    <div class="btn-gray">Редактировать</div>
                </div>
            </div>
            
            <div class="card" style="padding:10px">
                <div style="font-size:12px; color:#2a5885; margin-bottom:10px;">Друзья <span style="color:#939393">145</span></div>
                <div style="display:grid; grid-template-columns: 1fr 1fr 1fr; gap:5px; text-align:center; font-size:11px;">
                    <div><div style="width:50px; height:50px; background:#f0f2f5; margin:0 auto; border-radius:50%"></div>Маша</div>
                    <div><div style="width:50px; height:50px; background:#f0f2f5; margin:0 auto; border-radius:50%"></div>Саня</div>
                    <div><div style="width:50px; height:50px; background:#f0f2f5; margin:0 auto; border-radius:50%"></div>Кирилл</div>
                </div>
            </div>
        </div>

        <div class="col-right">
            <div class="card">
                <h2>Павел Дуров <span style="color:#939393; font-size:12px; float:right;">online</span></h2>
                <div class="status">Создаю будущее...</div>
                
                <div class="info-row"><div class="label">День рождения:</div><div class="value">10 октября 1984 г.</div></div>
                <div class="info-row"><div class="label">Город:</div><div class="value">Санкт-Петербург</div></div>
                <div class="info-row"><div class="label">Языки:</div><div class="value">Русский, English, Français</div></div>
                
                <div class="stats">
                    <a class="stat-item"><div class="stat-num">145</div><div class="stat-label">друзей</div></a>
                    <a class="stat-item"><div class="stat-num">2.4M</div><div class="stat-label">подписчиков</div></a>
                    <a class="stat-item"><div class="stat-num">12</div><div class="stat-label">фотографий</div></a>
                    <a class="stat-item"><div class="stat-num">8</div><div class="stat-label">отметок</div></a>
                </div>
            </div>

            <div class="card post-input">
                <textarea id="postText" rows="1" placeholder="Что у Вас нового?" onclick="this.rows=3"></textarea>
                <div id="btnBlock" style="display:none">
                    <button class="post-btn" onclick="addPost()">Отправить</button>
                </div>
            </div>
            
            <div id="wall">
                <!-- Здесь будут посты -->
            </div>
        </div>
    </main>
</div>

<script>
    // Раскрытие поля ввода
    document.getElementById('postText').onclick = function() {
        this.rows = 3;
        document.getElementById('btnBlock').style.display = 'block';
    };

    function addPost() {
        const text = document.getElementById('postText').value;
        if (!text) return;
        
        const wall = document.getElementById('wall');
        const post = document.createElement('div');
        post.className = 'card wall-post';
        post.innerHTML = `
            <div class="mini-ava"></div>
            <div class="post-body">
                <div class="post-author">Павел Дуров</div>
                <div class="post-txt">${text}</div>
                <div style="color:#939393; font-size:11px; margin-top:8px;">Только что</div>
            </div>
        `;
        wall.prepend(post);
        document.getElementById('postText').value = '';
        document.getElementById('postText').rows = 1;
        document.getElementById('btnBlock').style.display = 'none';
    }
</script>

</body>
</html>

