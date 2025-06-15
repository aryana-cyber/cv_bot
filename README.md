import logging
from aiogram import Bot, Dispatcher, types
from aiogram.utils import executor
import os
from dotenv import load_dotenv

load_dotenv()
BOT_TOKEN = os.getenv("BOT_TOKEN")

bot = Bot(token=BOT_TOKEN)
dp = Dispatcher(bot)

@dp.message_handler(commands=["start"])
async def send_welcome(message: types.Message):
    await message.reply("Bot berhasil dijalankan!")

if __name__ == "__main__":
    logging.basicConfig(level=logging.INFO)
    executor.start_polling(dp, skip_updates=True)
