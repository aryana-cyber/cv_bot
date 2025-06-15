import os
from aiogram import Bot, Dispatcher, executor
from dotenv import load_dotenv
from handler import register_handlers

# Load token dari file .env
load_dotenv()
TOKEN = os.getenv("7968292523:AAE5p5Fm2hm56gpogQUv_ZKWJerJsjLfrVY")

# Pastikan token terisi
if not TOKEN:
    raise ValueError("7968292523:AAE5p5Fm2hm56gpogQUv_ZKWJerJsjLfrVY")

# Inisialisasi bot dan dispatcher
bot = Bot(token=TOKEN)
dp = Dispatcher(bot)

# Register semua handler dari handler.py
register_handlers(dp)

# Jalankan bot
if __name__ == "__main__":
    executor.start_polling(dp, skip_updates=True)
