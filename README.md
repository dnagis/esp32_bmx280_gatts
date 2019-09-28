# bmx280 présenté en gatt (plus mainstream)

départ: esp-idf/examples/get-started/hello_world

# bmx280
ajouter le dir "components/bmx280" qui contient include/bmx280.h (adapter l'address 0x76 ou 0x77)
	tu peux alors #include "i2c_bme280.h" et "bmx280.h"
importé le code de esp32_bmp280_i2c -> 
lecture capteur triggered par callback, c'est pas toi qui actionne -> donc updater une variable

# gatts
esp-idf/examples/bluetooth/bluedroid/ble/gatt_server
make menuconfig -> activer components/bluetooth

# lecture bluez
gatttool -b 30:AE:A4:04:C3:5A --char-read -a 0x002a

parsing en bash:
retour_gatttool=`/initrd/mnt/dev_save/packages/bluez-5.50/attrib/gatttool -b 30:AE:A4:04:C3:5A --char-read -a 0x002a`
read -ra ARRAY <<< "${retour_gatttool#"Characteristic value/descriptor: "}" #syntaxe array:${ARRAY[0]}
printf "temp: %02d.%02d\n" $((0x${ARRAY[0]})) $((0x${ARRAY[1]})) #${ARRAY[2]} = temp_pos
printf "pres: %i.%02d\n" `expr $((0x${ARRAY[3]})) + 872` $((0x${ARRAY[4]}))
printf "hum: %02d.%02d\n" $((0x${ARRAY[5]})) $((0x${ARRAY[6]})) 



