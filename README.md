from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes

TOKEN = "7968292523:AAHnfMKboYQq6BQfqWTNxl_DLc5474JJk4o"

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Halo! Kirim file CSV-mu, nanti aku proses.")

if __name__ == "__main__":
    app = ApplicationBuilder().token(7968292523:AAHnfMKboYQq6BQfqWTNxl_DLc5474JJk4o).build()
    app.add_handler(CommandHandler("start", start))
    print("Bot sedang berjalan...")
    app.run_polling()
