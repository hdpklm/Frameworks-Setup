# ESP32 Init Progect

set P=COM27
C:\hassan\programs\esp\esp-idf\install.bat
C:\hassan\programs\esp\esp-idf\export.bat

## create project
idf.py create-project test-encrypt-1
cd test-encrypt-1

## configure project
set IDF_TARGET=esp32s3
idf.py set-target esp32s3
idf.py menuconfig

## monitor in concole, with format
idf.py monitor
