- extboard_key_test.bin  
esp-idf/examples/peripherals/adc/single_read/single_read

- extboard_sdcard_test.bin  
esp-idf/examples/storage/sd_card/sdspi

- voice_recognition  
esp-who/examples/esp32-s3-eye  
cn: "Hi lexin"  
en: "Hi ESP"  
use the esp32-s3 native USB CDC to check the log output.  
flash command line: esptool.py --chip esp32s3 -p /dev/ttyACM0 -b 460800 --before=default_reset --after=hard_reset --no-stub write_flash --flash_mode dio --flash_freq 80m --flash_size 8MB 0x0 bootloader/bootloader.bin 0x10000 esp32-s3-eye.bin 0x8000 partition_table/partition-table.bin 0x3f8000 model.bin

- extboard_face_recognition.bin  
esp-who/examples/human_face_detection/terminal
use the esp32-s3 native USB CDC to check the log output.  

- extboard_pmod_lcd_v1.1_ili9341.bin & extboard_pmod_lcd_v1.2_ili9341.bin  
/home/pi/oss/esp-idf/examples/peripherals/spi_master/lcd
