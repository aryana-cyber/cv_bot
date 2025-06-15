import logging
import os
from dotenv import load_dotenv
from telegram import Update, InputFile
from telegram.ext import (
    ApplicationBuilder,
    CommandHandler,
    MessageHandler,
    ContextTypes,
    filters,
)
from converter import txt_to_vcf

load_dotenv()
TOKEN = os.getenv("TELEGRAM_TOKEN")

logging.basicConfig(
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s", level=logging.INFO
)

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Halo! Kirim file .txt untuk dikonversi ke .vcf ✨")

async def handle_document(update: Update, context: ContextTypes.DEFAULT_TYPE):
    document = update.message.document
    if document.mime_type != "text/plain":
        await update.message.reply_text("Harap kirim file .txt saja.")
        return

    file = await context.bot.get_file(document.file_id)
    file_path = document.file_name
    await file.download_to_drive(file_path)

    vcf_path = "output.vcf"
    txt_to_vcf(file_path, vcf_path)

    await update.message.reply_document(document=InputFile(vcf_path))

    os.remove(file_path)
    os.remove(vcf_path)

if __name__ == "__main__":
    app = ApplicationBuilder().token(TOKEN).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.Document.ALL, handle_document))

    print("Bot berjalan...")
    app.run_polling()
