# ESP32-S3 Encrypt

espefuse -p com24 summary

## changes in menuconfig
1. security features -> Enable hardware Secure Boot in bootloader -> Checked
2. security features -> Enable flash encryption boot -> Checked
3. security features -> Enable flash encryption boot -> Enable usage mode -> developer
4. security features -> Potentially insecure options -> Leave unused digest slots
5. security features -> Potentially insecure options -> Require flash encryption to be already enabled -> unChecked
6. partition table -> partition table -> Custom partition table CSV
7. partition table -> offset -> 0xD000

## secure boot
```sh
espsecure generate_signing_key secure_boot_signing_key.pem
idf.py fullclean
idf.py bootloader
idf.py build
```

## create encryption key
```sh
espsecure digest_private_key --keyfile secure_boot_signing_key.pem key.bin
```

## create and burn encryption key, use only one of the following options
```sh
# create encryption key AES-128 (256-bit key)
espsecure generate_flash_encryption_key my_flash_encryption_key.bin
espefuse.py --port PORT burn_key flash_encryption my_flash_encryption_key.bin
```

## upload and encrypt efuses
```sh
idf.py bootloader-flash
idf.py flash
```

## sign app, not encript it, only add signature to the end of the file
```sh
espsecure sign_data --version 1 --keyfile secure_boot_signing_key.pem --output signed/app.bin ./test.app/main.ino.bin
espsecure sign_data --keyfile ./secure_boot_signing_key.pem --output ./signed/table.bin ./build/partition_table/partition-table.bin
espsecure sign_data --keyfile ./secure_boot_signing_key.pem --output ./signed/app.bin ./build/ESP32-Encrypt.bin
```

## manual encrypt
```sh
espsecure encrypt_flash_data --keyfile ./my_flash_encryption_key.bin --address 0x0D000 --output ./encrypt/table.bin ./build/partition_table/partition-table.bin
espsecure encrypt_flash_data --keyfile ./my_flash_encryption_key.bin --address 0x10000 --output ./encrypt/app.bin ./build/ESP32-Encrypt.bin
```

## upload encrypt app
```sh
esptool --chip esp32 write_flash 0xD000 ./encrypt/table.bin --force
esptool --chip esp32 write_flash 0x10000 ./encrypt/app.bin --force
```

## upload not encrypted app
```sh
esptool --chip esp32 write_flash --encrypt 0x00000 ./build/bootloader/bootloader.bin --force
esptool --chip esp32 write_flash --encrypt 0x0D000 ./build/partition_table/partition-table.bin --force
esptool --chip esp32 write_flash --encrypt 0x0E000 ./build/ota_data_initial.bin --force
esptool --chip esp32 write_flash --encrypt 0x10000 ./build/ESP32-Encrypt.bin --force
```

## read flash enc
```sh
esptool --chip esp32 --no-stub read_flash 0x00000 0x0B000 ./reads/bootloader.bin
esptool --chip esp32 --no-stub read_flash 0x0D000 0x00C00 ./reads/table.bin
esptool --chip esp32 --no-stub read_flash 0x0E000 0x02000 ./reads/ota-data.bin
esptool --chip esp32 --no-stub read_flash 0x10000 0xC1000 ./reads/app.bin
```

## upload plaintext, encrypt inside the esp32
```sh
esptool --chip esp32 --no-stub write_flash --encrypt 0x00000 ./build/bootloader/bootloader.bin --force
esptool --chip esp32 --no-stub write_flash --encrypt 0x0D000 ./build/partition_table/partition-table.bin --force
esptool --chip esp32 --no-stub write_flash --encrypt 0x10000 ./build/ESP32-Encrypt.bin --force
```

## change to Release mode
```sh
espefuse.py -p COM7 burn_efuse DIS_DOWNLOAD_MANUAL_ENCRYPT 1
```
