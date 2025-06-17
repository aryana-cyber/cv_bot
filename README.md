import os
from telegram import Update
from telegram.ext import ContextTypes
from converter import convert_txt_to_vcf

# Fungsi /start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "Hai! 👋 Kirim file .txt atau .csv kamu.\n"
        "Setelah itu aku bakal tanya format nama file output .vcf-nya ✨"
    )

# Fungsi menangani dokumen
async def handle_document(update: Update, context: ContextTypes.DEFAULT_TYPE):
    document = update.message.document

    # Validasi jenis file
    if not document.file_name.endswith((".txt", ".csv")):
        await update.message.reply_text("⚠️ Hanya mendukung file .txt atau .csv ya!")
        return

    # Buat folder download jika belum ada
    os.makedirs("downloads", exist_ok=True)

    # Simpan file ke folder download
    file_path = os.path.join("downloads", document.file_name)
    file = await context.bot.get_file(document.file_id)
    await file.download_to_drive(file_path)

    # Simpan file path ke user_data
    context.user_data["file_path"] = file_path

    # Tanya ke user nama file output-nya
    await update.message.reply_text("✅ File diterima!\n\nKetik nama file .vcf yang kamu mau (tanpa spasi, tanpa .vcf):")
