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
esp-idf/examples/peripherals/spi_master/lcd

pi@raspberrypi:~/oss/esp-idf $ git diff examples/peripherals/spi_master/lcd/main/spi_master_example_main.c
diff --git a/examples/peripherals/spi_master/lcd/main/spi_master_example_main.c b/examples/peripherals/spi_master/lcd/main/spi_master_example_main.c
index c3fdc8e336..92cf1e04ce 100644
--- a/examples/peripherals/spi_master/lcd/main/spi_master_example_main.c
+++ b/examples/peripherals/spi_master/lcd/main/spi_master_example_main.c
@@ -50,6 +50,35 @@
#define PIN_NUM_DC   4
#define PIN_NUM_RST  5
#define PIN_NUM_BCKL 6
+#elif defined CONFIG_IDF_TARGET_ESP32S3
+#define LCD_HOST    SPI2_HOST
+
+/* PMOD-LCD-v1.1 */
+#if 1
+#define PIN_NUM_MISO 14
+#define PIN_NUM_MOSI 4
+#define PIN_NUM_CLK  48
+#define PIN_NUM_CS   5
+
+#define PIN_NUM_DC   46
+#define PIN_NUM_RST  47
+#define PIN_NUM_BCKL 21
+#endif
+
+
+/* PMOD-LCD-v1.2 */
+#if 0
+#define PIN_NUM_MISO 46
+#define PIN_NUM_MOSI 47
+#define PIN_NUM_CLK  21
+#define PIN_NUM_CS   14
+
+#define PIN_NUM_DC   0
+#define PIN_NUM_RST  45
+#define PIN_NUM_BCKL 48
+#endif
+
+
#elif defined CONFIG_IDF_TARGET_ESP32C3
#define LCD_HOST    SPI2_HOST
