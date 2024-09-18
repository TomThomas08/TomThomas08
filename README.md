

const TelegramBot = require('node-telegram-bot-api');
const axios = require('axios');

// Telegram bot tokenini o'z tokeningiz bilan almashtiring
const token = '7478726912:AAEJDdyq_8zodmk6qLW5UiLO21W2KyIhqxI'; // Telegram bot tokeni
const bot = new TelegramBot(token, { polling: true });

// Google Custom Search API sozlamalari
const googleApiKey = 'AIzaSyALG3-KDtaWhjjYSonUuvYkBXMZ4YRq9HE'; // Google API kaliti
const searchEngineId = '2301570ce47984ecd'; // Sizning Custom Search Engine ID

// Google qidiruv URL
const googleSearchUrl = `https://www.googleapis.com/customsearch/v1?key=${googleApiKey}&cx=${searchEngineId}&q=`;

// Savolga javob topish funksiyasi
async function getGoogleAnswer(query) {
  try {
    const response = await axios.get(googleSearchUrl + encodeURIComponent(query));
    const items = response.data.items;
    if (items && items.length > 0) {
      // Birinchi natija sarlavhasini va qisqa tavsifini olish
      return `${items[0].title}\n${items[0].snippet}\n${items[0].link}`;
    } else {
      return 'Javob topilmadi.';
    }
  } catch (error) {
    return 'Qidiruvda xatolik yuz berdi.';
  }
}

// Botning /start komandasiga javob
bot.onText(/\/start/, (msg) => {
  const chatId = msg.chat.id;
  bot.sendMessage(chatId, 'Salom! Savolingizni yuboring, men savolingizga javob tariqasida  sizga sayt tashlayman.');
});

// Savolga javob berish
bot.on('message', async (msg) => {
  const chatId = msg.chat.id;
  const text = msg.text;

  // Agar foydalanuvchi matn yuborsa
  if (text && !text.startsWith('/')) {
    const answer = await getGoogleAnswer(text);
    bot.sendMessage(chatId, answer);
  }
});
bot.on('message', (msg) => {
    const chatId = msg.chat.id;
    const text = msg.text.toLowerCase(); // Xabar matnini kichik harflarga o'tkazish
  
    if (text === 'salom ') {
      bot.sendMessage(chatId, 'Salom');
    } else if (text !== '/start') {
      bot.sendMessage(chatId, '' + msg.text);
    }
  });
