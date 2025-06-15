from telegram import Update, File
from telegram.ext import ContextTypes
from converter import convert_txt_to_vcf
import os

# Fungsi untuk menangani perintah /start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "Hai! 👋 Kirim file .txt kamu, nanti akan aku ubah jadi file .vcf (vCard kontak)."
    )

# Fungsi untuk menangani dokumen/file
async def handle_document(update: Update, context: ContextTypes.DEFAULT_TYPE):
    document = update.message.document

    # Cek apakah file yang dikirim adalah file .txt
    if document.mime_type != 'text/plain':
        await update.message.reply_text("⚠️ Format file tidak didukung. Kirim file .txt saja.")
        return

    # Download file ke folder sementara
    file_path = f"temp/{document.file_name}"
    os.makedirs("temp", exist_ok=True)

    telegram_file: File = await context.bot.get_file(document.file_id)
    await telegram_file.download_to_drive(file_path)

    # Ubah file txt ke vcf
    vcf_path = convert_txt_to_vcf(file_path)

    # Kirim hasil vcf ke user
    await update.message.reply_document(document=open(vcf_path, "rb"))

    # Hapus file sementara
    os.remove(file_path)
    os.remove(vcf_path)
