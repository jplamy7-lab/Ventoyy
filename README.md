Python
import os
import subprocess

def create_ventoy_clone(device):
    print(f"--- Préparation de {device} (TYPE VENTOY) ---")
    
    # 1. Nettoyage du disque (ATTENTION : Efface tout)
    os.system(f"wipefs -a {device}")

    # 2. Création des partitions
    # P1: Données (ISO) - Reste du disque
    # P2: Boot (EFI) - 32MB à la fin
    print("[*] Partitionnement...")
    subprocess.run(["parted", "-s", device, "mklabel", "gpt"])
    subprocess.run(["parted", "-s", device, "mkpart", "primary", "ext4", "1MiB", "-32MiB"])
    subprocess.run(["parted", "-s", device, "mkpart", "primary", "fat32", "-32MiB", "100%"])
    subprocess.run(["parted", "-s", device, "set", "2", "esp", "on"])

    # 3. Formatage
    print("[*] Formatage...")
    os.system(f"mkfs.ext4 -L 'MES_ISO' {device}1")
    os.system(f"mkfs.vfat -F 32 -n 'VTOY_BOOT' {device}2")

    print("\n[!] Structure prête. Il faut maintenant installer GRUB.")

# Utilisation : remplacez /dev/sdX par votre clé USB (ex: /dev/sdb)
# create_ventoy_clone("/dev/sdb")
