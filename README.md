import os

def convert_to_vcf(input_file, jumlah_kontak, jumlah_file, nama_kontak, nama_file, angka_awal):
    hasil_paths = []

    with open(input_file, "r", encoding="utf-8") as f:
        lines = [line.strip() for line in f.readlines() if line.strip()]

    if len(lines) < jumlah_kontak:
        jumlah_kontak = len(lines)

    kontak_per_file = jumlah_kontak // jumlah_file
    sisa = jumlah_kontak % jumlah_file

    os.makedirs("output", exist_ok=True)

    index = 0
    for i in range(jumlah_file):
        banyak_kontak = kontak_per_file + (1 if i < sisa else 0)
        nama_vcf = f"{nama_file}_{angka_awal + i}.vcf"
        path_vcf = os.path.join("output", nama_vcf)

        with open(path_vcf, "w", encoding="utf-8") as vcf:
            for _ in range(banyak_kontak):
                if index >= jumlah_kontak:
                    break

                line = lines[index]
                index += 1

                if "," in line:
                    nama, nomor = line.split(",", 1)
                elif ":" in line:
                    nama, nomor = line.split(":", 1)
                else:
                    continue

                nama = nama.strip()
                nomor = nomor.strip()

                vcf.write("BEGIN:VCARD\n")
                vcf.write("VERSION:3.0\n")
                vcf.write(f"N:{nama};;;\n")
                vcf.write(f"FN:{nama_kontak} {nama}\n")
                vcf.write(f"TEL;TYPE=CELL:{nomor}\n")
                vcf.write("END:VCARD\n\n")

        hasil_paths.append(path_vcf)

    return hasil_paths
