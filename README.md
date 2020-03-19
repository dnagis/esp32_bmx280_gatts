# bm[x]280 servi en gatt

esp-idf/examples/bluetooth/bluedroid/ble/gatt_server
pour l'explication services char descriptor je l'ai mise dans le java de bluevvnx (android_bt_java) et dans morphotox/bluetooth


# bmx280
Attention il faut ajouter le dir "components/bmx280" depuis mon repo https://github.com/dnagis/esp32_bmp280_i2c
	nécessaire pour #include "i2c_bme280.h" et "bmx280.h"
	les réglages se font dans components/bmx280/include/bmx280.h (adapter l'address 0x76 ou 0x77 pour i2c et les pins SDA et SCL)
	
# Design
lecture capteur triggered par callback, c'est pas toi qui actionne -> donc updater une variable

# gatts
Attention make menuconfig -> activer components/bluetooth



# Modes de transmission: 

1) Soit l'observer lit avec char-read:
en bluez: gatttool -b 30:AE:A4:04:C3:5A --char-read -a 0x002a
en java: mBluetoothGatt.readCharacteristic(mCharacteristic);

puis bluez parsing en bash:
retour_gatttool=`/initrd/mnt/dev_save/packages/bluez-5.50/attrib/gatttool -b 30:AE:A4:04:C3:5A --char-read -a 0x002a`  
read -ra ARRAY <<< "${retour_gatttool#"Characteristic value/descriptor: "}" #syntaxe array:${ARRAY[0]}  
printf "`date` temp: %02d.%02d pres: %i.%02d hum: %02d.%02d\n" $((0x${ARRAY[0]})) $((0x${ARRAY[1]})) `expr $((0x${ARRAY[3]})) + 872` $((0x${ARRAY[4]})) $((0x${ARRAY[5]})) $((0x${ARRAY[6]}))

2) Soit le broadcaster émet une notification: passer le bool NOTIFICATIONS à true (dans les defs au début) et l'interval en ms se set dans env_sensors_init()
esp_ble_gatts_send_indicate(...) que je mets dans la callback "nouvelles données senseur" env_sensor_callback()

