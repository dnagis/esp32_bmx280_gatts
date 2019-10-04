# bmx280 présenté en gatt (plus mainstream)

départ: esp-idf/examples/get-started/hello_world

# bmx280
Attention il faut ajouter le dir "components/bmx280" qui contient include/bmx280.h (adapter l'address 0x76 ou 0x77)
	tu peux alors #include "i2c_bme280.h" et "bmx280.h"
importé le code de esp32_bmp280_i2c -> 
lecture capteur triggered par callback, c'est pas toi qui actionne -> donc updater une variable

# gatts
Attention make menuconfig -> activer components/bluetooth
esp-idf/examples/bluetooth/bluedroid/ble/gatt_server


# Reception bluez
Via Read char:
gatttool -b 30:AE:A4:04:C3:5A --char-read -a 0x002a

parsing en bash:
retour_gatttool=`/initrd/mnt/dev_save/packages/bluez-5.50/attrib/gatttool -b 30:AE:A4:04:C3:5A --char-read -a 0x002a`  
read -ra ARRAY <<< "${retour_gatttool#"Characteristic value/descriptor: "}" #syntaxe array:${ARRAY[0]}  
printf "`date` temp: %02d.%02d pres: %i.%02d hum: %02d.%02d\n" $((0x${ARRAY[0]})) $((0x${ARRAY[1]})) `expr $((0x${ARRAY[3]})) + 872` $((0x${ARRAY[4]})) $((0x${ARRAY[5]})) $((0x${ARRAY[6]}))

Via notification: 
esp_ble_gatts_send_indicate(...) que je mets dans la callback "nouvelles données senseur" env_sensor_callback()

