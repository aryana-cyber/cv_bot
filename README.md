import logging
from telegram.ext import ApplicationBuilder, MessageHandler, CommandHandler, filters
from handler import start, handle_document, handle_reply

# Aktifkan logging (penting buat debugging)
logging.basicConfig(
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    level=logging.INFO
)

# Ganti token ini dengan token bot kamu
BOT_TOKEN = "7954326302:AAH5TRKvfyW8WZq33Qad0E0icl6lo9SgtiA"

def main():
    app = ApplicationBuilder().token(BOT_TOKEN).build()

    # Command /start
    app.add_handler(CommandHandler("start", start))

    # Kalau user kirim file txt/csv
    app.add_handler(MessageHandler(filters.Document.FILE_EXTENSION("txt") | filters.Document.FILE_EXTENSION("csv"), handle_document))

    # Kalau user reply format nama VCF
    app.add_handler(MessageHandler(filters.TEXT & filters.REPLY, handle_reply))

    print("Bot sudah jalan...")
    app.run_polling()

if __name__ == '__main__':
    main()
