import os
from telegram import Update
from telegram.ext import ContextTypes
from converter import convert_to_vcf

UPLOAD_DIR = "downloads"
os.makedirs(UPLOAD_DIR, exist_ok=True)

async def handle_document(update: Update, context: ContextTypes.DEFAULT_TYPE):
    document = update.message.document
    file = await context.bot.get_file(document.file_id)
    file_path = os.path.join(UPLOAD_DIR, document.file_name)
    await file.download_to_drive(file_path)

    with open(file_path, 'r', encoding='utf-8') as f:
        contacts = [line.strip() for line in f.readlines() if line.strip()]

    jumlah_kontak = len(contacts)

    context.user_data['file_path'] = file_path
    context.user_data['file_name'] = document.file_name
    context.user_data['jumlah_kontak'] = jumlah_kontak

    await update.message.reply_text(
        f"File '{document.file_name}' telah diunggah.\n"
        f"Jumlah Kontak: {jumlah_kontak}. Silakan reply file ini dengan format:\n"
        f"<nama kontak>,<nama file vcf>,<jumlah kontak>,<jumlah file>,<angka untuk nama file>"
    )

async def handle_reply(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if 'file_path' not in context.user_data:
        await update.message.reply_text("❌ Tidak ada file yang sedang diproses. Kirim file dulu.")
        return

    try:
        format_str = update.message.text.strip()
        file_path = context.user_data['file_path']
        output_dir = "outputs"
        os.makedirs(output_dir, exist_ok=True)

        vcf_files = convert_to_vcf(file_path, format_str, output_dir)

        for file in vcf_files:
            with open(file, 'rb') as f:
                await update.message.reply_document(f)

        await update.message.reply_text("✅ File berhasil dikonversi dan dikirim.")

    except Exception as e:
        print(e)
        await update.message.reply_text("❌ Terjadi kesalahan saat mengubah file.")
