# tugasASDjos

from prettytable import PrettyTable
from pwinput import pwinput
from datetime import datetime

orderan = {}
akun_member = {
    "jo": {"password": "067", "is_member": True},
    "bulan": {"password": "047", "is_member": True},
    "kul": {"password": "057", "is_member": True}  
}

menu_pelanggan = {
    1: {"Layanan": "Cuci sepatu", "Harga": 50000},
    2: {"Layanan": "Treatment sepatu", "Harga": 70000},
    3: {"Layanan": "Repaint sepatu", "Harga": 120000},
    4: {"Layanan": "Repair sepatu", "Harga": 100000}
}

saldo = {
    "jo": 500000,
    "bulan": 300000,
    "kul": 100000
}

epay = {
    "jo": 100000,
    "bulan": 100000,
    "kul": 100000
}

def cek_jam():
    now = datetime.now()
    jam_sekarang = now.hour
    if 8 <= jam_sekarang < 16:
        return True 
    else:
        return False  

def top_up(username):
    try:
        jumlah_topup = float(input("Masukkan jumlah saldo yang ingin di-top up: "))
        if jumlah_topup > 0:
            if saldo[username] >= jumlah_topup:
                epay[username] += jumlah_topup
                saldo[username] -= jumlah_topup
                print(f"Top-up berhasil! Saldo epay Anda sekarang Rp. {epay[username]}")
            else:
                print("Saldo tidak mencukupi untuk top-up.")
        else:
            print("Jumlah top-up harus lebih dari 0.")
    except ValueError:
        print("Masukkan jumlah top-up yang valid.")

def cek_saldo(username):
    if username in saldo:
        print(f"Saldo Anda saat ini: Rp. {saldo[username]}")
    else:
        print("Saldo Anda saat ini: Rp. 0")

    if username in epay:
        print(f"Saldo ePay Anda saat ini: Rp. {epay[username]}")
    else:
        print("Saldo ePay Anda saat ini: Rp. 0")

def kurangi_saldo(username, jumlah):
    if username in saldo:
        saldo[username] -= jumlah

def cek_epay(username):
    if username in epay:
        return epay[username]
    else:
        return 0

def kurangi_epay(username, jumlah):
    if username in epay:
        epay[username] -= jumlah

def menu_admin():
    print("Menu admin sedang dalam pengembangan.")

def menu_utama(username):
    while True:
        if not cek_jam():
            print("Maaf, layanan tidak tersedia saat ini. Silakan kembali antara jam 08:00 dan 16:00.")
            exit()
        print("""
        ====================================
        SELAMAT DATANG DI LAUNDRY SEPATU!
        ====================================
        Menu Utama:
        1. Admin
        2. Pelanggan
        3. Top-up Epay
        4. Cek Saldo
        5. Keluar
        """)
        tindakan = input("Silahkan pilih tindakan: ")
        if tindakan == "1":
            menu_admin()
        elif tindakan == "2":
            menu_pelanggan_func(username)
        elif tindakan == "3":
            top_up(username)
        elif tindakan == "4":
            cek_saldo(username)
        elif tindakan == "5":
            print("Terima kasih!")
            exit()
        else:
            print("Input tidak valid!")

def menu_pelanggan_func(username):
    pesanan_pelanggan = []
    while True:
        try:
            print("\nMenu Pelanggan:")
            table = PrettyTable()
            table.field_names = ["No", "Layanan", "Harga"]
            for key, value in menu_pelanggan.items():
                table.add_row([key, value['Layanan'], f"Rp. {value['Harga']}"])
            print(table)
            print("0 | Lanjutkan")
            print("[tekan Enter untuk kembali]")
            pilihan = input("Pilih nomor layanan : ")

            if pilihan == "":
                menu_utama(username)
                break

            elif pilihan == "0":
                if not pesanan_pelanggan:
                    print("Pesanan anda kosong")
                    continue
                else:
                    break
            elif 1 <= int(pilihan) <= len(menu_pelanggan):
                pesanan_pelanggan.append(int(pilihan))
            else:
                print("Input tidak valid!")
        except ValueError:
            print("Input tidak valid!")

    while True:
        if not pesanan_pelanggan:
            print("\n!!! Pesanan anda kosong !!!")
            menu_pelanggan_func(username)
        else:
            print("\nDaftar pesanan anda :")
            for i, pesanan in enumerate(pesanan_pelanggan, start=1):
                print(f"{i}. {menu_pelanggan[pesanan]['Layanan']} - Rp. {menu_pelanggan[pesanan]['Harga']}")

            pilihan = input("Pilih nomor pesanan yang ingin dibatalkan atau pilih 0 untuk lanjutkan : ")
            if pilihan == "":
                break
            elif pilihan == "0":
                menu_pembayaran(pesanan_pelanggan, username)
            elif 1 <= int(pilihan) <= len(menu_pelanggan):
                pesanan_pelanggan.pop(int(pilihan) - 1)
            else:
                print("Input tidak valid!")
                break

def menu_pembayaran(pesanan_pelanggan, username):
    harga_pesanan = sum(menu_pelanggan[pesanan]['Harga'] for pesanan in pesanan_pelanggan)

    while True:
        print("\nMenu Pembayaran:")
        print("1. Dengan akun member")
        print("2. Tanpa akun member")
        print("3. Kembali")

        pilihan = input("Pilih tindakan (1-3): ")

        if pilihan == "1":
            password = pwinput("Masukkan password: ")
            if username in akun_member and akun_member[username]['password'] == password and akun_member[username]['is_member']:
                total_harga = harga_pesanan * 0.7
                print(f"Pesanan Anda mendapatkan diskon member 30% menjadi Rp. {total_harga}")

                if cek_epay(username) >= total_harga:
                    kurangi_epay(username, total_harga)
                    print("Pembayaran berhasil!")
                    break
                else:
                    print("Saldo ePay tidak mencukupi untuk pembayaran.")
            else:
                print("Username atau password tidak valid atau bukan member.")
        elif pilihan == "2":
            kode_voucher = input("Masukkan kode voucher (jika ada): ")
            if kode_voucher == "DISKON30":
                total_harga = harga_pesanan * 0.7
                print(f"Diskon voucher 30% berhasil diterapkan. Total harga: Rp. {total_harga}")

                if cek_epay(username) >= total_harga:
                    kurangi_epay(username, total_harga)
                    print("Pembayaran berhasil!")
                    break
                else:
                    print("Saldo ePay tidak mencukupi untuk pembayaran.")
            else:
                total_harga = harga_pesanan
                if kode_voucher:
                    print("Kode voucher tidak valid.")
                else:
                    if cek_epay(username) >= total_harga:
                        kurangi_epay(username, total_harga)
                        print("Pembayaran berhasil!")
                        break
                    else:
                        print("Saldo ePay tidak mencukupi untuk pembayaran.")
        elif pilihan == "3":
            menu_pelanggan_func(username)
            break
        else:
            print("Input tidak valid!")

# Starting point of the application
if _name_ == "_main_":
    print("=== Login ===")
    username = input("Username: ")
    password = pwinput("Password: ")

    if username in akun_member and akun_member[username]['password'] == password:
        print("Login berhasil!")
        menu_utama(username)
    else:
        print("Username atau password salah.")
