# Fungsi menangani balasan user (reply untuk format file)
async def handle_reply(update: Update, context: ContextTypes.DEFAULT_TYPE):
    format_str = update.message.text.strip()
    file_path = context.user_data.get("file_path")

    if not file_path:
        await update.message.reply_text("❗ Maaf, aku belum nerima file apa pun sebelumnya.")
        return

    output_dir = "output"
    os.makedirs(output_dir, exist_ok=True)

    try:
        # ✅ Panggil fungsi convert_to_vcf (bukan convert_txt_to_vcf)
        from converter import convert_to_vcf
        vcf_files = convert_to_vcf(file_path, format_str, output_dir)

        # Kirim file .vcf satu per satu
        for vcf_file in vcf_files:
            with open(vcf_file, "rb") as f:
                await update.message.reply_document(f)

        await update.message.reply_text("✅ Semua file .vcf sudah aku kirim!")

    except Exception as e:
        await update.message.reply_text(f"❗ Terjadi error saat mengubah file: {e}")
