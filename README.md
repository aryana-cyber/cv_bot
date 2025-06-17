import os
from aiogram import types
from aiogram.types import Message
from converter import convert_to_vcf


user_files = {}  # Simpan data file per user

async def handle_start(message: Message):
    await message.reply("Halo! Kirim file .txt atau .csv untuk saya ubah ke .vcf.")

async def handle_document(message: Message):
    document = message.document
    file_name = document.file_name

    if not file_name.endswith(('.txt', '.csv')):
        await message.reply("Maaf, hanya file .txt atau .csv yang didukung.")
        return

    file_path = f"downloads/{message.from_user.id}_{file_name}"
    os.makedirs("downloads", exist_ok=True)

    await message.bot.download(file=document, destination=file_path)

    # Hitung jumlah kontak
    with open(file_path, "r", encoding="utf-8") as f:
        lines = f.readlines()
    jumlah_kontak = len(lines)

    # Simpan info user dan file
    user_files[message.from_user.id] = {
        "path": file_path,
        "jumlah_kontak": jumlah_kontak
    }

    # Balas dengan instruksi reply
    await message.reply(
        f"File '{file_name}' telah diunggah.\n"
        f"Jumlah Kontak: {jumlah_kontak}. Silakan reply file ini dengan format:\n"
        f"<nama kontak>,<nama file vcf>,<jumlah kontak>,<jumlah file>,<angka untuk nama file>"
    )

async def handle_reply(message: Message):
    if message.reply_to_message and message.reply_to_message.document:
        user_id = message.from_user.id
        if user_id not in user_files:
            await message.reply("File tidak ditemukan. Silakan kirim ulang.")
            return

        # Ambil info file user
        file_info = user_files[user_id]
        file_path = file_info["path"]

        try:
            nama_kontak, nama_file, jumlah_kontak, jumlah_file, angka_awal = message.text.strip().split(",")
            jumlah_kontak = int(jumlah_kontak)
            jumlah_file = int(jumlah_file)
            angka_awal = int(angka_awal)
        except Exception:
            await message.reply("Format salah. Gunakan format:\n<nama kontak>,<nama file vcf>,<jumlah kontak>,<jumlah file>,<angka untuk nama file>")
            return

        # Konversi
        hasil_paths = convert_to_vcf(
            input_file=file_path,
            jumlah_kontak=jumlah_kontak,
            jumlah_file=jumlah_file,
            nama_kontak=nama_kontak,
            nama_file=nama_file,
            angka_awal=angka_awal
        )

        for path in hasil_paths:
            await message.reply_document(types.InputFile(path))

        await message.reply("File VCF telah dibuat dan dikirim.")
