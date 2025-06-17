# Fungsi menangani balasan teks setelah file diunggah
async def handle_reply(update: Update, context: ContextTypes.DEFAULT_TYPE):
    # Ambil file_path dari user_data
    file_path = context.user_data.get("file_path")
    if not file_path:
        await update.message.reply_text("⚠️ Kamu belum kirim file .txt atau .csv.")
        return

    # Ambil format string dari pesan user
    format_str = update.message.text.strip()

    # Buat folder output jika belum ada
    output_dir = "output"
    os.makedirs(output_dir, exist_ok=True)

    try:
        # Panggil fungsi konversi
        from converter import convert_to_vcf  # pastikan nama fungsi sesuai
        vcf_files = convert_to_vcf(file_path, format_str, output_dir)

        if not vcf_files:
            await update.message.reply_text("❌ Gagal mengubah file. Pastikan format yang kamu kirim benar.")
            return

        # Kirim semua file .vcf hasil konversi
        for vcf_file in vcf_files:
            with open(vcf_file, "rb") as f:
                await update.message.reply_document(document=f)

        await update.message.reply_text("✅ Selesai! Semua file .vcf sudah dikirim.")
    except Exception as e:
        print(f"Error: {e}")
        await update.message.reply_text("❌ Terjadi kesalahan saat mengubah file.")
