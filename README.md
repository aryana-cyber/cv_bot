import os
from aiogram import Bot, Dispatcher, executor
from dotenv import load_dotenv
from handler import register_handlers

# Load token dari file .env
load_dotenv()
TOKEN = os.getenv("BOT_TOKEN")

# Pastikan token terisi
if not TOKEN:
    raise ValueError("BOT_TOKEN tidak ditemukan di file .env")

# Inisialisasi bot dan dispatcher
bot = Bot(token=TOKEN)
dp = Dispatcher(bot)

# Register semua handler dari handler.py
register_handlers(dp)

# Jalankan bot
if __name__ == "__main__":
    executor.start_polling(dp, skip_updates=True)
