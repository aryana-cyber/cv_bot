import os
from telegram import Update
from telegram.ext import ApplicationBuilder, MessageHandler, CommandHandler, ContextTypes, filters
from utils.converter import txt_to_vcf

BOT_TOKEN = "7968292523:AAE5p5Fm2hm56gpogQUv_ZKWJerJsjLfrVY"

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Halo! Kirim file .txt dan aku akan ubah jadi .vcf 👌")

async def handle_document(update: Update, context: ContextTypes.DEFAULT_TYPE):
    document = update.message.document
    if document.file_name.endswith(".txt"):
        file = await context.bot.get_file(document.file_id)
        txt_path = f"{document.file_name}"
        await file.download_to_drive(txt_path)

        vcf_path = txt_path.replace(".txt", ".vcf")
        txt_to_vcf(txt_path, vcf_path)

        await update.message.reply_document(document=open(vcf_path, "rb"))
        os.remove(txt_path)
        os.remove(vcf_path)
    else:
        await update.message.reply_text("Kirim file .txt saja ya!")

if __name__ == '__main__':
    app = ApplicationBuilder().token(7968292523:AAE5p5Fm2hm56gpogQUv_ZKWJerJsjLfrVY).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.Document.ALL, handle_document))

    print("Bot jalan...")
    app.run_polling()
