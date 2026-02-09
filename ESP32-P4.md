# ESP32P4について

~~## LPコアのMailBoxについて~~
~~ulp_lp_core_intr_enable~~
~~でLPコアの割り込みを有効にすると以下でスタックする。~~
```
lp_core_mailbox_send
lp_core_mailbox_send_async
lp_core_mailbox_receive
lp_core_mailbox_receive_async
```
~~非同期か同期かによらずスタックする。~~
~~送受信もしていないのでHPコアも場合によっては一生待つ。~~

https://github.com/espressif/esp-idf/issues/18095
修正された。BIG THANKS

## MIPI-DSIについて
ESP-IDF5系列と6系列では若干APIが変更されている。
6系列ブランチでは仕様変更に追い付いておらず、また、画面表示に不具合が生じるため、masterブランチの使用が望ましい。

## ESP-ConsoleとUSB CDCについて
ESP-STDIOの出力先にUSB CDCが表示されない。
S3に変更すると表示される。
ESP-STDIOのKconfigに以下があるが、P4を追加してもだめ
[components/esp_stdio/Kconfig](https://github.com/espressif/esp-idf/blob/master/components/esp_stdio/Kconfig)
```
config ESP_CONSOLE_USB_CDC
    bool "USB CDC"
    # && !TINY_USB is because the ROM CDC driver is currently incompatible with TinyUSB.
    depends on (IDF_TARGET_ESP32S2 || IDF_TARGET_ESP32S3)  && !TINY_USB
```
また、ESP32-P4のesp_romにはUSB関連のコードが存在しない。
[components/esp_rom/esp32p4/include/esp32p4/rom](https://github.com/espressif/esp-idf/tree/master/components/esp_rom/esp32p4/include/esp32p4/rom)

## ESP-ConsoleとTinyUSB CDCについて
BLOCKINGをサポートしていないので使えない。
[device/esp_tinyusb/vfs_tinyusb.c](https://github.com/espressif/esp-usb/blob/master/device/esp_tinyusb/vfs_tinyusb.c)
```
s_vfstusb.flags = flags | O_NONBLOCK; // for now only non-blocking mode is implemented
```

## example/https_requestで何をしてもエラーがでる。
CMakeLists.txt内の下記を削除すると問題ない。
```
list(APPEND sdkconfig_defaults $ENV{IDF_PATH}/components/mbedtls/config/mbedtls_preset_default.conf)
```

```
CMake Error at C:/esp/v6.0-beta2/esp-idf/tools/cmake/build.cmake:598 (cmake_parse_arguments):
  Syntax error in cmake code at

    C:/esp/v6.0-beta2/esp-idf/tools/cmake/build.cmake:598

  when parsing string

    SDKCONFIG_DEFAULTS;C:\esp\v6.0-beta2\esp-idf/components/mbedtls/config/mbedtls_preset_default.conf;C:/https_request/sdkconfig.defaults;SDKCONFIG;C:/https_request/sdkconfig;BUILD_DIR;C:/https_request/build;PROJECT_NAME;https_request;PROJECT_DIR;C:/https_request;PROJECT_VER;1;COMPONENTS;main;

  Invalid character escape '\s'.
```
エラーから類推すると、WindowsとLinuxのパス文字の取り扱いの差異でエラーが出ている。
