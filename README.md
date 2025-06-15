def txt_to_vcf(txt_file_path, vcf_file_path):
    with open(txt_file_path, 'r', encoding='utf-8') as txt_file:
        lines = txt_file.readlines()

    with open(vcf_file_path, 'w', encoding='utf-8') as vcf_file:
        for line in lines:
            line = line.strip()
            if not line:
                continue

            if ',' in line:
                name, phone = line.split(',', 1)
            elif ':' in line:
                name, phone = line.split(':', 1)
            else:
                continue

            name = name.strip()
            phone = phone.strip()

            vcf_file.write("BEGIN:VCARD\n")
            vcf_file.write("VERSION:3.0\n")
            vcf_file.write(f"N:{name};;;\n")
            vcf_file.write(f"FN:{name}\n")
            vcf_file.write(f"TEL;TYPE=CELL:{phone}\n")
            vcf_file.write("END:VCARD\n\n")
