<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Таймер | Синхронизация по ссылке</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
        }
        body {
            min-height: 100vh;
            background: linear-gradient(135deg, #0a0f1a 0%, #05070f 100%);
            font-family: 'Segoe UI', 'Courier New', monospace;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        .timer-container {
            background: rgba(5, 15, 25, 0.75);
            backdrop-filter: blur(14px);
            border-radius: 3rem;
            padding: 1.8rem 1.5rem 2rem;
            box-shadow: 0 30px 50px rgba(0, 0, 0, 0.6);
            border: 1px solid rgba(0, 200, 255, 0.4);
            width: 100%;
            max-width: 1000px;
        }
        .timer-display {
            background: #01070f;
            padding: 1.8rem 1rem;
            border-radius: 2rem;
            text-align: center;
            border: 1px solid #2a6f8f;
            margin-bottom: 1.8rem;
        }
        .days-block {
            font-size: clamp(2rem, 9vw, 4.2rem);
            font-weight: 900;
            background: linear-gradient(135deg, #ffe6b0, #ffbc6e);
            background-clip: text;
            -webkit-background-clip: text;
            color: #ffcf8a;
            text-shadow: 0 0 12px #ff9f2e;
            margin-bottom: 10px;
        }
        .time-digits {
            font-size: clamp(1.8rem, 8vw, 3.8rem);
            font-weight: 800;
            font-family: 'Monaco', 'Courier New', monospace;
            color: #00f2ff;
            text-shadow: 0 0 8px #00a6ff;
            letter-spacing: 2px;
        }
        .unit-label {
            display: flex;
            justify-content: center;
            gap: 1rem;
            flex-wrap: wrap;
            margin-top: 12px;
        }
        .unit-label span {
            background: #0f1f2c;
            padding: 0.3rem 1rem;
            border-radius: 40px;
            font-size: 0.8rem;
            font-weight: 600;
            color: #b8defa;
        }
        .controls {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 1rem;
            margin: 1.8rem 0 1.2rem;
        }
        button {
            background: #111e2a;
            border: none;
            font-family: inherit;
            font-weight: bold;
            font-size: 1rem;
            padding: 0.7rem 1.6rem;
            border-radius: 3rem;
            color: #cde5ff;
            cursor: pointer;
            transition: 0.2s;
            box-shadow: 0 3px 6px rgba(0, 0, 0, 0.4);
            border-bottom: 2px solid #2f6f8a;
        }
        button:active { transform: scale(0.96); }
        button.primary { background: #0f405a; border-bottom-color: #3cd0ff; color: white; }
        button.warning { background: #3a2a1a; border-bottom-color: #da9430; }
        .presets {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 0.6rem;
            margin: 0.5rem 0 1rem;
        }
        .preset-btn {
            background: #09212e;
            font-size: 0.85rem;
            padding: 0.45rem 1rem;
            border-bottom-color: #3680a0;
        }
        .big-day { background: #1f3a2a; border-bottom-color: #e0a34b; }
        .custom-panel {
            background: #03121c60;
            border-radius: 2rem;
            padding: 1rem;
            margin-top: 1rem;
        }
        .custom-input {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            gap: 0.6rem;
            margin: 0.8rem 0;
        }
        .custom-input input {
            background: #010f18;
            border: 1px solid #2f7f9f;
            padding: 0.6rem 0.8rem;
            border-radius: 2.5rem;
            width: 95px;
            text-align: center;
            color: #caf2ff;
            font-family: monospace;
            font-size: 1rem;
            font-weight: bold;
            outline: none;
        }
        .status-text {
            text-align: center;
            margin-top: 1rem;
            font-size: 0.85rem;
            color: #7d9cbb;
        }
        .share-section {
            margin-top: 1rem;
            text-align: center;
            padding-top: 0.8rem;
            border-top: 1px solid #2a4c6e;
        }
        .share-btn {
            background: #1a4c3a;
            border-bottom-color: #6fbf6f;
            margin-top: 0.5rem;
        }
        .link-container {
            margin-top: 0.8rem;
            display: flex;
            flex-wrap: wrap;
            gap: 0.5rem;
            justify-content: center;
            align-items: center;
        }
        .link-input {
            background: #010f18;
            border: 1px solid #2f7f9f;
            padding: 0.6rem 1rem;
            border-radius: 2rem;
            color: #caf2ff;
            font-family: monospace;
            font-size: 0.8rem;
            width: 70%;
            min-width: 200px;
            outline: none;
            text-align: center;
        }
        .copy-btn {
            background: #0f405a;
            border-bottom-color: #3cd0ff;
            padding: 0.6rem 1.2rem;
            font-size: 0.85rem;
        }
        footer { text-align: center; font-size: 0.7rem; margin-top: 1.2rem; color: #2f556b; }
    </style>
</head>
<body>
<div class="timer-container">
    <div class="timer-display">
        <div class="days-block" id="daysDisplay">-- дней</div>
        <div class="time-digits" id="timerDisplay">--:--:--</div>
        <div class="unit-label">
            <span>ДНИ</span><span>ЧАСЫ</span><span>МИНУТЫ</span><span>СЕКУНДЫ</span>
        </div>
    </div>

    <div class="controls">
        <button id="startBtn" class="primary">▶ СТАРТ</button>
        <button id="pauseBtn">⏸ ПАУЗА</button>
        <button id="resetBtn" class="warning">⟳ СБРОС</button>
    </div>

    <div class="presets">
        <button class="preset-btn" data-days="0" data-hours="0" data-mins="1">1 мин</button>
        <button class="preset-btn" data-days="0" data-hours="0" data-mins="5">5 мин</button>
        <button class="preset-btn" data-days="0" data-hours="1" data-mins="0">1 час</button>
        <button class="preset-btn" data-days="1" data-hours="0" data-mins="0">1 день</button>
        <button class="preset-btn" data-days="7" data-hours="0" data-mins="0">7 дней</button>
        <button class="preset-btn" data-days="30" data-hours="0" data-mins="0">30 дней</button>
        <button class="preset-btn big-day" data-days="150" data-hours="0" data-mins="0">🔥 150 дней</button>
        <button class="preset-btn" data-days="365" data-hours="0" data-mins="0">365 дней</button>
    </div>

    <div class="custom-panel">
        <div class="custom-input">
            <input type="number" id="customDays" placeholder="Дни" min="0" value="150">
            <input type="number" id="customHours" placeholder="Часы" min="0" max="23" value="0">
            <input type="number" id="customMinutes" placeholder="Мин" min="0" max="59" value="0">
            <input type="number" id="customSeconds" placeholder="Сек" min="0" max="59" value="0">
            <button id="setCustomBtn" class="preset-btn">УСТАНОВИТЬ</button>
        </div>
        <div class="status-text" id="statusMsg">⚡ Таймер готов</div>
    </div>

    <div class="share-section">
        <button id="generateLinkBtn" class="share-btn">🔗 СГЕНЕРИРОВАТЬ ССЫЛКУ (с текущим состоянием)</button>
        <div id="linkContainer" class="link-container" style="display: none;">
            <input type="text" id="shareLink" class="link-input" readonly>
            <button id="copyLinkBtn" class="copy-btn">📋 КОПИРОВАТЬ</button>
        </div>
    </div>
    <footer>⏱️ Таймер не останавливается. Ссылка передаёт точное состояние (оставшееся время и запуск).</footer>
</div>

<script>
    // ========== ЭЛЕМЕНТЫ ==========
    const daysSpan = document.getElementById('daysDisplay');
    const timerDisplay = document.getElementById('timerDisplay');
    const startBtn = document.getElementById('startBtn');
    const pauseBtn = document.getElementById('pauseBtn');
    const resetBtn = document.getElementById('resetBtn');
    const presetBtns = document.querySelectorAll('.preset-btn');
    const setCustomBtn = document.getElementById('setCustomBtn');
    const customDays = document.getElementById('customDays');
    const customHours = document.getElementById('customHours');
    const customMinutes = document.getElementById('customMinutes');
    const customSeconds = document.getElementById('customSeconds');
    const statusSpan = document.getElementById('statusMsg');
    const generateLinkBtn = document.getElementById('generateLinkBtn');
    const linkContainer = document.getElementById('linkContainer');
    const shareLink = document.getElementById('shareLink');
    const copyLinkBtn = document.getElementById('copyLinkBtn');

    // ========== СОСТОЯНИЕ ==========
    let savedTotalSeconds = 150 * 86400;   // установленное время (для сброса)
    let endTime = null;                   // unix-секунда окончания
    let isRunning = false;
    let timerInterval = null;

    // ========== ВСПОМОГАТЕЛЬНЫЕ ==========
    function formatTime(seconds) {
        if (seconds < 0) seconds = 0;
        const days = Math.floor(seconds / 86400);
        const hours = Math.floor((seconds % 86400) / 3600);
        const minutes = Math.floor((seconds % 3600) / 60);
        const secs = seconds % 60;
        let dayWord = "дней";
        if (days % 10 === 1 && days % 100 !== 11) dayWord = "день";
        else if ([2,3,4].includes(days % 10) && ![12,13,14].includes(days % 100)) dayWord = "дня";
        daysSpan.innerText = `${days} ${dayWord}`;
        timerDisplay.innerText = `${hours.toString().padStart(2,'0')}:${minutes.toString().padStart(2,'0')}:${secs.toString().padStart(2,'0')}`;
    }

    function getRemaining() {
        if (!endTime) return 0;
        const now = Math.floor(Date.now() / 1000);
        return Math.max(0, endTime - now);
    }

    function updateDisplay() {
        if (isRunning && endTime) {
            formatTime(getRemaining());
            if (getRemaining() === 0) {
                stopTimer();
                statusSpan.innerText = "🔔 ВРЕМЯ ВЫШЛО! 🔔";
            }
        } else if (endTime) {
            formatTime(getRemaining());
        } else {
            formatTime(savedTotalSeconds);
        }
    }

    function stopTimer() {
        if (timerInterval) clearInterval(timerInterval);
        isRunning = false;
        endTime = null;
        saveState();
        if (timerInterval) clearInterval(timerInterval);
        timerInterval = setInterval(updateDisplay, 1000);
        updateDisplay();
    }

    // ========== СОХРАНЕНИЕ В LOCALSTORAGE ==========
    function saveState() {
        const state = {
            savedTotalSeconds: savedTotalSeconds,
            endTime: endTime,
            isRunning: isRunning
        };
        localStorage.setItem('timerState', JSON.stringify(state));
    }

    function loadState() {
        const raw = localStorage.getItem('timerState');
        if (!raw) return false;
        try {
            const state = JSON.parse(raw);
            savedTotalSeconds = state.savedTotalSeconds;
            endTime = state.endTime;
            isRunning = state.isRunning;
            if (isRunning && endTime && getRemaining() <= 0) {
                isRunning = false;
                endTime = null;
                saveState();
            }
            return true;
        } catch(e) { return false; }
    }

    // ========== УСТАНОВКА НОВОГО ВРЕМЕНИ ==========
    function setNewTime(days, hours, minutes, seconds, fromUrl = false, remainingSeconds = null, running = false) {
        let total = (days * 86400) + (hours * 3600) + (minutes * 60) + seconds;
        if (total < 1) total = 1;
        savedTotalSeconds = total;

        const now = Math.floor(Date.now() / 1000);
        if (remainingSeconds !== null && remainingSeconds > 0) {
            // восстанавливаем точное состояние из ссылки
            endTime = now + remainingSeconds;
            isRunning = running;
        } else {
            // обычная установка: ставим на паузу с полным временем
            endTime = now + total;
            isRunning = false;
        }

        if (timerInterval) clearInterval(timerInterval);
        timerInterval = setInterval(updateDisplay, 1000);
        updateDisplay();
        saveState();

        customDays.value = days;
        customHours.value = hours;
        customMinutes.value = minutes;
        customSeconds.value = seconds;

        if (!fromUrl) {
            statusSpan.innerText = `✓ установлено: ${days}д ${hours.toString().padStart(2,'0')}:${minutes.toString().padStart(2,'0')}:${seconds.toString().padStart(2,'0')}`;
        } else {
            statusSpan.innerText = `📦 загружено из ссылки: ${days}д ${hours.toString().padStart(2,'0')}:${minutes.toString().padStart(2,'0')}:${seconds.toString().padStart(2,'0')} | ${isRunning ? "идет" : "пауза"}`;
        }
    }

    // ========== УПРАВЛЕНИЕ ==========
    function startTimer() {
        if (isRunning) return;
        let remaining = getRemaining();
        if (remaining <= 0) {
            statusSpan.innerText = "⛔ время вышло, установите новое";
            return;
        }
        if (!endTime) {
            const now = Math.floor(Date.now() / 1000);
            endTime = now + remaining;
        }
        isRunning = true;
        saveState();
        statusSpan.innerText = "▶ таймер запущен";
    }

    function pauseTimer() {
        if (!isRunning) {
            statusSpan.innerText = "⏸ таймер не активен";
            return;
        }
        const remaining = getRemaining();
        if (remaining > 0) {
            const now = Math.floor(Date.now() / 1000);
            endTime = now + remaining;
        } else {
            endTime = null;
        }
        isRunning = false;
        saveState();
        updateDisplay();
        statusSpan.innerText = "⏸ пауза";
    }

    function resetTimer() {
        const now = Math.floor(Date.now() / 1000);
        endTime = now + savedTotalSeconds;
        isRunning = false;
        saveState();
        updateDisplay();
        const days = Math.floor(savedTotalSeconds / 86400);
        const hours = Math.floor((savedTotalSeconds % 86400) / 3600);
        const minutes = Math.floor((savedTotalSeconds % 3600) / 60);
        const secs = savedTotalSeconds % 60;
        customDays.value = days;
        customHours.value = hours;
        customMinutes.value = minutes;
        customSeconds.value = secs;
        statusSpan.innerText = `⟳ сброс к ${days}д ${hours.toString().padStart(2,'0')}:${minutes.toString().padStart(2,'0')}:${secs.toString().padStart(2,'0')}`;
    }

    // ========== ГЕНЕРАЦИЯ ССЫЛКИ (передаём всё состояние) ==========
    function generateShareLink() {
        // текущее оставшееся время
        let remaining = getRemaining();
        if (remaining <= 0) remaining = savedTotalSeconds; // если время вышло, используем установленное
        const days = Math.floor(savedTotalSeconds / 86400);
        const hours = Math.floor((savedTotalSeconds % 86400) / 3600);
        const minutes = Math.floor((savedTotalSeconds % 3600) / 60);
        const secs = savedTotalSeconds % 60;
        // добавляем параметры: установленное время (d,h,m,s), оставшееся (r), запущен (run)
        const url = `${window.location.origin}${window.location.pathname}?d=${days}&h=${hours}&m=${minutes}&s=${secs}&r=${Math.floor(remaining)}&run=${isRunning ? 1 : 0}`;
        shareLink.value = url;
        linkContainer.style.display = "flex";
        statusSpan.innerText = "🔗 ссылка готова! Отправь другу — состояние сохранится";
    }

    function copyLink() {
        shareLink.select();
        document.execCommand('copy');
        statusSpan.innerText = "✅ ссылка скопирована!";
    }

    // ========== ЗАГРУЗКА ИЗ URL ==========
    function loadFromURL() {
        const urlParams = new URLSearchParams(window.location.search);
        const d = parseInt(urlParams.get('d'));
        const h = parseInt(urlParams.get('h'));
        const m = parseInt(urlParams.get('m'));
        const s = parseInt(urlParams.get('s'));
        const r = parseInt(urlParams.get('r'));
        const run = parseInt(urlParams.get('run'));
        if (!isNaN(d) && !isNaN(h) && !isNaN(m) && !isNaN(s)) {
            let remaining = (!isNaN(r) && r > 0) ? r : null;
            let running = (run === 1);
            setNewTime(d, h, m, s, true, remaining, running);
            return true;
        }
        return false;
    }

    // ========== ИНИЦИАЛИЗАЦИЯ ==========
    function initialize() {
        if (loadFromURL()) return;
        if (loadState()) {
            // восстановили из localStorage
            if (!endTime) {
                const now = Math.floor(Date.now() / 1000);
                endTime = now + savedTotalSeconds;
            }
            if (timerInterval) clearInterval(timerInterval);
            timerInterval = setInterval(updateDisplay, 1000);
            updateDisplay();
            const days = Math.floor(savedTotalSeconds / 86400);
            const hours = Math.floor((savedTotalSeconds % 86400) / 3600);
            const minutes = Math.floor((savedTotalSeconds % 3600) / 60);
            const secs = savedTotalSeconds % 60;
            customDays.value = days;
            customHours.value = hours;
            customMinutes.value = minutes;
            customSeconds.value = secs;
            statusSpan.innerText = isRunning ? "▶ таймер идёт" : "⏸ таймер на паузе";
            return;
        }
        // по умолчанию 150 дней
        setNewTime(150, 0, 0, 0, false);
        statusSpan.innerText = "🔥 готов | таймер на 150 дней";
    }

    // ========== ОБРАБОТЧИКИ ==========
    startBtn.onclick = startTimer;
    pauseBtn.onclick = pauseTimer;
    resetBtn.onclick = resetTimer;
    generateLinkBtn.onclick = generateShareLink;
    copyLinkBtn.onclick = copyLink;

    presetBtns.forEach(btn => {
        btn.onclick = () => {
            const days = parseInt(btn.getAttribute('data-days')) || 0;
            const hours = parseInt(btn.getAttribute('data-hours')) || 0;
            const mins = parseInt(btn.getAttribute('data-mins')) || 0;
            setNewTime(days, hours, mins, 0);
        };
    });

    setCustomBtn.onclick = () => {
        let days = parseInt(customDays.value) || 0;
        let hours = parseInt(customHours.value) || 0;
        let mins = parseInt(customMinutes.value) || 0;
        let secs = parseInt(customSeconds.value) || 0;
        if (days === 0 && hours === 0 && mins === 0 && secs === 0) {
            statusSpan.innerText = "❌ задайте время больше нуля";
            return;
        }
        setNewTime(days, hours, mins, secs);
    };

    initialize();
</script>
</body>
</html>
