// --- ВСТАВЬ СЮДА ССЫЛКУ НА СВОЙ БЭКЕНД (БОТА) ---
// Если ты тестируешь локально с ngrok, ссылка будет типа:
// "https://your-ngrok-subdomain.ngrok.io"
// Если ты на VDS, это "http://YOUR_SERVER_IP:8080"
// ВАЖНО: Если бэкенд на HTTP, а фронтенд на HTTPS, будут проблемы.
// В идеале, и то, и то должно быть за HTTPS.
const API_BASE_URL = "http://YOUR_SERVER_IP:8080"; 

// Глобальные переменные
let currentCardIndex = 0;
let userCards = [];
let isFlipped = false;

// Элементы DOM
const loadingEl = document.getElementById('loading');
const cardContainerEl = document.getElementById('card-container');
const cardEl = document.getElementById('card');
const cardTermEl = document.getElementById('card-term');
const cardDefinitionEl = document.getElementById('card-definition');
const controlsEl = document.getElementById('controls');
const noCardsEl = document.getElementById('no-cards');
const flipButton = document.getElementById('flip-button');
const nextButton = document.getElementById('next-button');

// Инициализация Telegram Web App
const tg = window.Telegram.WebApp;
tg.ready(); // Сообщаем Telegram, что приложение готово
tg.expand(); // Растягиваем приложение на весь экран

// --- Основная функция ---
async function main() {
    try {
        // 1. Получаем ID юзера из Telegram
        if (!tg.initDataUnsafe || !tg.initDataUnsafe.user) {
            showError("Не удалось получить данные Telegram. Откройте приложение через бота.");
            return;
        }
        const userId = tg.initDataUnsafe.user.id;
        
        // 2. Загружаем карточки с нашего бэкенда (Python)
        const response = await fetch(`${API_BASE_URL}/api/get_cards?user_id=${userId}`);
        if (!response.ok) {
            throw new Error('Ошибка сети при загрузке карточек');
        }
        const data = await response.json();

        // 3. Обрабатываем карточки
        if (data.cards && data.cards.length > 0) {
            userCards = data.cards;
            shuffleArray(userCards); // Перемешиваем
            showCard(currentCardIndex);
            showUiElements(true);
        } else {
            // Карточек нет
            showUiElements(false);
        }

    } catch (error) {
        console.error(error);
        showError(error.message || "Неизвестная ошибка");
    }
}

// Показываем/скрываем UI
function showUiElements(hasCards) {
    loadingEl.style.display = 'none';
    if (hasCards) {
        cardContainerEl.style.display = 'block';
        controlsEl.style.display = 'block';
        noCardsEl.style.display = 'none';
    } else {
        cardContainerEl.style.display = 'none';
        controlsEl.style.display = 'none';
        noCardsEl.style.display = 'block';
    }
}

// Показать ошибку
function showError(message) {
    loadingEl.innerText = message;
    loadingEl.style.color = 'red';
}

// Показать карточку по индексу
function showCard(index) {
    if (!userCards[index]) return;
    
    // Сбрасываем переворот
    if (isFlipped) {
        cardEl.classList.remove('is-flipped');
        isFlipped = false;
    }
    
    cardTermEl.innerText = userCards[index].term;
    cardDefinitionEl.innerText = userCards[index].definition;
}

// Перемешать массив (алгоритм Фишера-Йетса)
function shuffleArray(array) {
    for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
    }
}

// --- Обработчики событий ---
flipButton.addEventListener('click', () => {
    isFlipped = !isFlipped;
    cardEl.classList.toggle('is-flipped');
});

nextButton.addEventListener('click', () => {
    currentCardIndex++;
    if (currentCardIndex >= userCards.length) {
        currentCardIndex = 0; // Начинаем заново
        shuffleArray(userCards); // И перемешиваем
    }
    showCard(currentCardIndex);
});

// Запускаем!
main();