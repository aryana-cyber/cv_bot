# Fungsi /start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "Hai 👋 Kirim file .txt atau .csv kamu...\n"
        "Setelah itu aku bakal tanya format nama file output .vcf-nya ✨"
    )

# Fungsi menangani dokumen
async def handle_document(update: Update, context: ContextTypes.DEFAULT_TYPE):
    document = update.message.document
    file_name = document.file_name

    if not file_name.endswith((".txt", ".csv")):
        await update.message.reply_text("⚠️ Hanya mendukung file .txt atau .csv ya!")
        return

    os.makedirs("downloads", exist_ok=True)
    file_path = os.path.join("downloads", file_name)

    file = await context.bot.get_file(document.file_id)
    await file.download_to_drive(file_path)

    context.user_data["file_path"] = file_path

    await update.message.reply_text(
        "✅ File berhasil diunduh.\n\n"
        "Sekarang kirim format nama kontak seperti ini (contoh):\n"
        "`{nama};{nomor}`\n\n"
        "Contoh format:\n"
        "`Arya Sastra;081234567890`\n\n"
        "Kalo udah, langsung kirim ya!",
        parse_mode="Markdown"
    )
