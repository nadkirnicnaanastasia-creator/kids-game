

<!DOCTYPE html>

<html lang="uk">

<head>

<meta charset="UTF-8">

<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Весела музична фізкультхвилинка</title>

<style>

* {

    box-sizing: border-box;

}

body {

    margin: 0;

    font-family: 'Trebuchet MS', sans-serif;

    text-align: center;

    background: linear-gradient(135deg, #fff3a6, #b6f1ff, #ffd4ec);

    min-height: 100vh;

    overflow-x: hidden;

    color: #493b70;

}

.container {

    max-width: 850px;

    margin: auto;

    padding: 20px;

}

h1 {

    font-size: clamp(26px, 5vw, 38px);

    color: #8052bd;

    text-shadow: 2px 2px white;

}

.subtitle {

    font-size: 19px;

}

.bear {

    font-size: 125px;

    margin: 10px;

    display: inline-block;

    animation: bounce 1.5s infinite;

    filter: drop-shadow(0 8px 5px #9995);

}

@keyframes bounce {

    0%, 100% { transform: translateY(0); }

    50% { transform: translateY(-18px); }

}

.jump {

    animation: jump .4s infinite alternate;

}

@keyframes jump {

    to { transform: translateY(-65px) rotate(10deg); }

}

.spin {

    animation: spin 1s infinite linear;

}

@keyframes spin {

    to { transform: rotate(360deg); }

}

.stretch {

    animation: stretch 1s infinite alternate;

}

@keyframes stretch {

    to { transform: scale(1.3, 1.5); }

}

.timer {

    font-size: 48px;

    font-weight: bold;

    color: #ff6384;

    background: white;

    display: inline-block;

    padding: 10px 35px;

    border-radius: 25px;

    box-shadow: 0 5px 12px #0002;

    margin: 10px;

}

.message {

    font-size: 23px;

    min-height: 60px;

    font-weight: bold;

    color: #7045a5;

    margin: 15px;

}

.buttons {

    display: flex;

    flex-wrap: wrap;

    justify-content: center;

    gap: 12px;

    margin: 20px 0;

}

button {

    border: none;

    padding: 17px 20px;

    border-radius: 20px;

    font-size: 17px;

    font-weight: bold;

    cursor: pointer;

    color: white;

    background: #a66be0;

    box-shadow: 0 5px 0 #7545a8;

    transition: .2s;

}

button:hover {

    transform: translateY(-4px);

    filter: brightness(1.08);

}

button:disabled {

    opacity: .55;

    cursor: not-allowed;

    transform: none;

}

.start {

    background: #ff8b68;

    box-shadow: 0 5px 0 #d85e43;

}

.reset {

    background: #54bda0;

    box-shadow: 0 5px 0 #318b70;

}

.ground {

    font-size: 35px;

    margin-top: 10px;

}

#confetti {

    position: fixed;

    inset: 0;

    width: 100%;

    height: 100%;

    pointer-events: none;

    z-index: 5;

}

</style>

</head>

<body>

<canvas id="confetti"></canvas>

<div class="container">

    <h1>🎵 Весела музична фізкультхвилинка 🎵</h1>

    <p class="subtitle">

        Привіт, малята! Наш ведмедик запрошує вас порухатися!

    </p>

    <div class="bear" id="bear">🐻</div>

    <div class="message" id="message">

        Привіт! Я ведмедик Топтик! Обирай вправу та рухайся зі мною!

    </div>

    <div class="timer" id="timer">20</div>

    <p>⏰ Секунд залишилося</p>

    <div class="buttons">

        <button onclick="startExercise('jump')">

            🐰 Пострибай як зайчик

        </button>

        <button onclick="startExercise('spin')">

            🌀 Покрутися як млин

        </button>

        <button onclick="startExercise('stretch')">

            ☀️ Потягнися до сонечка

        </button>

    </div>

    <div class="buttons">

        <button class="start" onclick="startExercise(currentExercise || 'jump')">

            ▶ Почати знову

        </button>

        <button class="reset" onclick="resetGame()">

            🔄 Спочатку

        </button>

    </div>

    <div class="ground">🌷 🌼 🌷 🌻 🌼 🌷</div>

</div>

<script>

const bear = document.getElementById("bear");

const timer = document.getElementById("timer");

const message = document.getElementById("message");

const canvas = document.getElementById("confetti");

const ctx = canvas.getContext("2d");

let time = 20;

let interval = null;

let currentExercise = "jump";

let particles = [];

let animationFrame = null;

const exercises = {

    jump: [

        "Стрибаймо, як маленькі зайчики! 🐰",

        "Вище ніжки! Молодці!",

        "Раз, два, три — весело стрибай!",

        "Наші зайчики дуже спритні!",

        "Ще трішечки! Ви супер!"

    ],

    spin: [

        "Крутимося, наче веселий млин! 🌀",

        "Покрутилися в один бік!",

        "А тепер обережно в інший!",

        "Які ви молодці!",

        "Продовжуємо рухатися!"

    ],

    stretch: [

        "Потягніться до сонечка! ☀️",

        "Ручки високо-високо!",

        "Тягнемося до хмаринок!",

        "А тепер опускаємо ручки!",

        "Чудово! Ви справжні чемпіони!"

    ]

};

function speak(text) {

    if ("speechSynthesis" in window) {

        speechSynthesis.cancel();

        const speech = new SpeechSynthesisUtterance(text);

        speech.lang = "uk-UA";

        speech.rate = 0.9;

        speech.pitch = 1.2;

        speechSynthesis.speak(speech);

    }

}

function startExercise(type) {

    clearInterval(interval);

    speechSynthesis.cancel();

    stopConfetti();

    currentExercise = type;

    time = 20;

    timer.textContent = time;

    bear.className = "bear";

    void bear.offsetWidth;

    bear.classList.add(type);

    const texts = exercises[type];

    let textIndex = 0;

    message.textContent = texts[0];

    speak(texts[0]);

    interval = setInterval(() => {

        time--;

        timer.textContent = time;

        if (time % 4 === 0 && time > 0) {

            const text = texts[

                Math.min(textIndex + 1, texts.length - 1)

            ];

            textIndex++;

            message.textContent = text;

            speak(text);

        }

        if (time <= 0) {

            finishExercise();

        }

    }, 1000);

}

function finishExercise() {

    clearInterval(interval);

    bear.className = "bear";

    bear.textContent = "🐻";

    timer.textContent = "0";

    message.textContent = "УРА! Ти неймовірний молодець! 🎉👏";

    speak("Ура! Молодець! Ти чудово виконав вправу!");

    playSound();

    startConfetti();

}

function resetGame() {

    clearInterval(interval);

    speechSynthesis.cancel();

    stopConfetti();

    time = 20;

    timer.textContent = "20";

    bear.className = "bear";

    message.textContent =

        "Привіт! Я ведмедик Топтик! Обирай вправу та рухайся зі мною!";

}

function playSound() {

    const AudioContext =

        window.AudioContext || window.webkitAudioContext;

    if (!AudioContext) return;

    const audio = new AudioContext();

    [523, 659, 784, 1046].forEach((frequency, index) => {

        const oscillator = audio.createOscillator();

        const gain = audio.createGain();

        oscillator.connect(gain);

        gain.connect(audio.destination);

        oscillator.frequency.value = frequency;

        oscillator.type = "sine";

        const start = audio.currentTime + index * 0.16;

        gain.gain.setValueAtTime(0.2, start);

        gain.gain.exponentialRampToValueAtTime(

            0.001, start + 0.35

        );

        oscillator.start(start);

        oscillator.stop(start + 0.35);

    });

}

function resizeCanvas() {

    canvas.width = window.innerWidth;

    canvas.height = window.innerHeight;

}

window.addEventListener("resize", resizeCanvas);

resizeCanvas();

function startConfetti() {

    particles = [];

    for (let i = 0; i < 150; i++) {

        particles.push({

            x: Math.random() * canvas.width,

            y: -Math.random() * canvas.height,

            size: Math.random() * 9 + 4,

            speed: Math.random() * 4 + 2,

            rotation: Math.random() * 360,

            color: [

                "#ff6384",

                "#ffce54",

                "#48cfad",

                "#ac92ec",

                "#4fc1e9"

            ][Math.floor(Math.random() * 5)]

        });

    }

    drawConfetti();

}

function drawConfetti() {

    ctx.clearRect(0, 0, canvas.width, canvas.height);

    particles.forEach(p => {

        ctx.save();

        ctx.translate(p.x, p.y);

        ctx.rotate(p.rotation * Math.PI / 180);

        ctx.fillStyle = p.color;

        ctx.fillRect(-p.size / 2, -p.size / 2,

                     p.size, p.size);

        ctx.restore();

        p.y += p.speed;

        p.rotation += 4;

        if (p.y > canvas.height) {

            p.y = -10;

            p.x = Math.random() * canvas.width;

        }

    });

    animationFrame = requestAnimationFrame(drawConfetti);

}

function stopConfetti() {

    if (animationFrame) {

        cancelAnimationFrame(animationFrame);

        animationFrame = null;

    }

    ctx.clearRect(0, 0, canvas.width, canvas.height);

    particles = [];

}

</script>

</body>

</html>
