# ESP32-S3 Encrypt

<!-- reset menuconfig -->
idf.py set-target esp32

espefuse -p com4 summary
set IDF_TARGET=esp32

idf.py menuconfig
## changes in menuconfig
1. security features -> Enable flash encryption boot -> Checked
2. security features -> Enable flash encryption boot -> Enable usage mode -> developer
3. security features -> Potentially insecure options -> Require flash encryption to be already enabled -> unChecked
4. security features -> Enable hardware Secure Boot in bootloader -> Checked
5. security features -> Secure bootloader mode -> Reflashable
6. partition table -> partition table -> Custom partition table CSV
7. partition table -> offset -> 0xE000

## secure boot
```sh
espsecure generate_signing_key secure_boot_signing_key.pem
idf.py fullclean
idf.py bootloader
idf.py build
idf.py monitor
```

## create encryption key
```sh
espsecure digest_private_key --keyfile secure_boot_signing_key.pem key.bin
```

## create and burn encryption key
```sh
# create encryption key AES-128 (256-bit key)
espsecure generate_flash_encryption_key my_flash_encryption_key.bin
espefuse --port PORT burn_key flash_encryption my_flash_encryption_key.bin
```

## reflash bootloader
> you have to enable reflash bootloader in menuconfig, and after first reset.
> you have to [Read the AES key from efuse block 2, in reversed byte order], then save in a file as bin. like:
> "secure-bootloader-digest.bin"
```sh
espefuse --port COM32 burn_key secure_boot_v1 secure-bootloader-digest.bin
```

## upload
```sh
idf.py bootloader-flash
idf.py flash
```

## sign app, not encript it, only add signature to the end of the file
```sh
espsecure digest_secure_bootloader --keyfile secure-bootloader-digest.bin --output signed/bootloader.bin ./build/bootloader/bootloader.bin
espsecure sign_data --version 1 --keyfile secure_boot_signing_key.pem --output signed/table.bin ./build/partition_table/partition-table.bin
espsecure sign_data --version 1 --keyfile secure_boot_signing_key.pem --output signed/app.bin ./test.app/main.ino.bin
espsecure sign_data --version 1 --keyfile secure_boot_signing_key.pem --output signed/app.bin ./build/ESP32-Encrypt.bin
```

## test signed file, only for app and data, not work for bootloader
espsecure.py verify_signature -v 1 --keyfile secure_boot_signing_key.pem ./signed/app.bin 

## manual encrypt
```sh
espsecure encrypt_flash_data --keyfile ./my_flash_encryption_key.bin --address 0x00000 --output ./encrypt/bootloader.bin ./signed/bootloader.bin
espsecure encrypt_flash_data --keyfile ./my_flash_encryption_key.bin --address 0x0D000 --output ./encrypt/table.bin ./build/partition_table/partition-table.bin
espsecure encrypt_flash_data --keyfile ./my_flash_encryption_key.bin --address 0x10000 --output ./encrypt/app.bin ./build/ESP32-Encrypt.bin
```

## upload encrypt app
```sh
esptool.py --chip esp32 write_flash 0x00000 ./encrypt/bootloader.bin --force
esptool.py --chip esp32 write_flash 0x0D000 ./encrypt/table.bin --force
esptool.py --chip esp32 write_flash 0x10000 ./encrypt/app.bin --force
```

## upload not encrypted app
```sh
esptool.py --chip esp32 write_flash --encrypt 0x01000 ./build/bootloader/bootloader.bin --force
esptool.py --chip esp32 write_flash --encrypt 0x0D000 ./build/partition_table/partition-table.bin --force
esptool.py --chip esp32 write_flash --encrypt 0x0E000 ./build/ota_data_initial.bin --force
esptool.py --chip esp32 write_flash --encrypt 0x10000 ./build/ESP32-Encrypt.bin --force
```

## read flash enc
```sh
esptool.py --chip esp32 --no-stub read_flash 0x01000 0x0D000 ./reads/bootloader1.bin
esptool.py --chip esp32 --no-stub read_flash 0x0E000 0x00C00 ./reads/table.bin
esptool.py --chip esp32 --no-stub read_flash 0x0E000 0x02000 ./reads/ota-data.bin
esptool.py --chip esp32 --no-stub read_flash 0x10000 0xC1000 ./reads/app.bin
########################################################################################
esptool.py --chip esp32 --no-stub write_flash 0x01000 ./reads/bootloader.bin --force
esptool.py --chip esp32 --no-stub write_flash 0x0E000 ./reads/table.bin --force
esptool.py --chip esp32 --no-stub write_flash 0x0E000 ./reads/ota-data.bin --force
esptool.py --chip esp32 --no-stub write_flash 0x10000 ./reads/app.bin --force
```

## upload plaintext, encrypt inside the esp32
```sh
# upload bootloader should by flash at address 0x1000
esptool.py --chip esp32 write_flash --encrypt 0x01000 ./build/bootloader/bootloader.bin --force
esptool.py --chip esp32 write_flash --encrypt 0x00000 ./build/bootloader/bootloader-reflash-digest.bin --force
esptool.py --chip esp32 write_flash --encrypt 0x0D000 ./build/partition_table/partition-table.bin --force
esptool.py --chip esp32 write_flash --encrypt 0x10000 ./build/ESP32-Encrypt.bin --force
```

## change to Release mode
```sh
espefuse.py -p COM7 burn_efuse DIS_DOWNLOAD_MANUAL_ENCRYPT 1
```

# no se para que sirve
secure-bootloader-key-256.bin


esptool.py
