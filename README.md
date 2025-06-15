import os
from aiogram import Bot, Dispatcher, executor
from dotenv import load_dotenv
from handler import register_handlers

load_dotenv()
TOKEN = os.getenv("7968292523:AAE5p5Fm2hm56gpogQUv_ZKWJerJsjLfrVY")

if not TOKEN:
    raise ValueError("7968292523:AAE5p5Fm2hm56gpogQUv_ZKWJerJsjLfrVY")

bot = Bot(token=TOKEN)
dp = Dispatcher(bot)

register_handlers(dp)

if __name__ == "__main__":
    executor.start_polling(dp, skip_updates=True)
