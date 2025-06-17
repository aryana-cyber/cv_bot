import os import logging from telegram import Update, InputFile from telegram.ext import Application, MessageHandler, filters, CommandHandler, ContextTypes, CallbackContext from handler import handle_txt_file, handle_reply from dotenv import load_dotenv

load_dotenv() logging.basicConfig(level=logging.INFO)

TOKEN = os.getenv("BOT_TOKEN")

Simpan file sementara dan pesan file_id

user_files = {}

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE): await update.message.reply_text("Halo! Kirim file .txt atau .csv kamu ke sini untuk mulai.")

def main(): app = Application.builder().token(TOKEN).build()

app.add_handler(CommandHandler("start", start))
app.add_handler(MessageHandler(filters.Document.ALL, handle_txt_file))
app.add_handler(MessageHandler(filters.TEXT & filters.REPLY, handle_reply))

app.run_polling()

if name == 'main': main()
