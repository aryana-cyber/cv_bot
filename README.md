import os
from telegram import Update, InputFile
from telegram.ext import ContextTypes
from converter import txt_to_vcf

async def handle_file(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if update.message.document:
        file = update.message.document

        if not file.file_name.endswith('.txt'):
            await update.message.reply_text("❌ Silakan kirim file dengan format `.txt`.")
            return

        await update.message.reply_text("✅ File diterima! Sedang diproses...")

        # Download file
        file_path = await file.get_file()
        input_file_path = f"downloads/{file.file_name}"
        os.makedirs("downloads", exist_ok=True)
        await file_path.download_to_drive(input_file_path)

        # Nama file output
        output_filename = file.file_name.replace(".txt", ".vcf")
        output_file_path = f"downloads/{output_filename}"

        try:
            txt_to_vcf(input_file_path, output_file_path)

            # Kirim file hasil
            with open(output_file_path, "rb") as vcf_file:
                await update.message.reply_document(
                    document=InputFile(vcf_file, filename=output_filename),
                    caption="✅ Berikut hasil konversi file .vcf kamu!"
                )
        except Exception as e:
            await update.message.reply_text(f"❌ Gagal mengonversi file: {e}")
