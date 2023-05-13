# ESP32-S3 Encrypt

espefuse -p com24 summary

## changes in menuconfig
1. security features -> Enable hardware Secure Boot in bootloader -> Checked
2. security features -> Enable flash encryption boot -> Checked
3. security features -> Enable flash encryption boot -> Enable usage mode -> developer
4. security features -> Potentially insecure options -> Leave unused digest slots
5. security features -> Potentially insecure options -> Require flash encryption to be already enabled -> unChecked
6. partition table -> Custom partition table CSV -> partition_tae-csv
7. partition table -> offset -> 0xD000

## secure boot
```sh
espsecure generate_signing_key --version 2 secure_boot_signing_key.pem
idf.py bootloader
idf.py build
```

## create encryption key
```sh
espsecure digest_private_key --keyfile secure_boot_signing_key.pem key.bin
```

## create and burn encryption key, use only one of the following options
## depend on the menuconfig option 1. security features -> Enable flash encryption boot -> Enable usage node
```sh
## option1: create encryption key AES-128 (256-bit key)
espsecure generate_flash_encryption_key my_flash_encryption_key.bin
espefuse burn_key BLOCK_KEY0 my_flash_encryption_key.bin XTS_AES_128_KEY

## option2: create encryption key AES-256 (512-bit key)
espsecure generate_flash_encryption_key --keylen 512 my_flash_encryption_key.bin
espefuse --port com24 burn_key BLOCK_KEY1 my_flash_encryption_key.bin XTS_AES_256_KEY
```

## upload and encrypt efuses
idf.py flash

## upload bootloader after uploading app and partition table
esptool --chip esp32s3 --no-stub write_flash --encrypt 0x0 build/bootloader/bootloader.bin --force

## sign app, not encript it, only add signature to the end of the file
```sh
espsecure sign_data --version 2 --keyfile ./my_flash_encryption_key.bin --output ./encrypt/table.bin ./build/partition_table/partition-table.bin
espsecure sign_data --version 2 --keyfile ./my_flash_encryption_key.bin --output ./encrypt/app.bin ./build/test-encrypt-2.bin
```

## manual encrypt
```sh
espsecure encrypt_flash_data --aes_xts --keyfile ./my_flash_encryption_key.bin --address 0x10000 --output ./encrypt/table.bin ./build/partition_table/partition-table.bin
espsecure encrypt_flash_data --aes_xts --keyfile ./my_flash_encryption_key.bin --address 0x10000 --output ./encrypt/app.bin ./build/test-encrypt-2.bin
```

## upload app
```sh
esptool --port com32 --chip esp32s3 --no-stub write_flash 0xD000 ./encrypt/table.bin --force
esptool --port com32 --chip esp32s3 --no-stub write_flash 0x10000 ./encrypt/app.bin --force
```

## upload not encrypted app
```sh
esptool --chip esp32s3 --no-stub write_flash --encrypt 0xD000 ./build/partition_table/partition-table.bin --force
esptool --chip esp32s3 --no-stub write_flash --encrypt 0xE000 ./build/ota_data_initial.bin --force
esptool --chip esp32s3 --no-stub write_flash --encrypt 0x10000 ./build/test-encrypt-2.bin --force
```

## encrypt esp32-s3
idf.py encrypted-flash
## encrypt esp32-s3 app
idf.py encrypted-app-flash


## read flash enc
```sh
esptool --chip esp32s3 --no-stub read_flash 0x00000 0x0B000 ./reads/bootloader.bin
esptool --chip esp32s3 --no-stub read_flash 0x0D000 0x00C00 ./reads/table.bin
esptool --chip esp32s3 --no-stub read_flash 0x0E000 0x02000 ./reads/ota-data.bin
esptool --chip esp32s3 --no-stub read_flash 0x10000 0xC1000 ./reads/app.bin
esptool --chip esp32s3 --no-stub read_flash 0x0D000 0xC4000 ./reads/app+table.bin
esptool --chip esp32s3 --no-stub read_flash 0x00000 0xD1000 ./reads/boot+app+table.bin
```


## use by idf.py --port com32 encrypted-flash
```sh
esptool esp32s3 -p com32 -b 460800 --before=default_reset --after=no_reset --no-stub write_flash --flash_mode dio --flash_freq 80m --flash_size keep --encrypt 0x10000 test-encrypt-2.bin 0xd000 partition_table/partition-table.bin 0xe000 ota_data_initial.bin
```

## upload, encrypt inside the esp32
```sh
esptool --chip esp32s3 --no-stub write_flash --encrypt 0x00000 ./build/bootloader/bootloader.bin --force
esptool --chip esp32s3 --no-stub write_flash --encrypt 0x0D000 ./build/partition_table/partition-table.bin --force
esptool --chip esp32s3 --no-stub write_flash --encrypt 0x10000 ./build/test-encrypt-2.bin --force
```

## change to Release mode
```sh
espefuse.py -p COM7 burn_efuse DIS_DOWNLOAD_MANUAL_ENCRYPT 1
```
