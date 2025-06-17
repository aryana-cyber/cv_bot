import os
from dotenv import load_dotenv
from telegram import Update
from telegram.ext import (
    ApplicationBuilder,
    CommandHandler,
    MessageHandler,
    ContextTypes,
    filters,
)
from handler import start, handle_document, handle_reply

# Load token dari .env
load_dotenv()
TOKEN = os.getenv("7954326302:AAH5TRKvfyW8WZq33Qad0E0icl6lo9SgtiA")

# Fungsi utama
def main():
    app = ApplicationBuilder().token(TOKEN).build()

    # Daftar handler
    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.Document.ALL, handle_document))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_reply))

    print("🚀 Bot sudah berjalan...")
    app.run_polling()

if __name__ == "__main__":
    main()
