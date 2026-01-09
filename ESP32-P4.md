# ESP32P4について

## LPコアのMailBoxについて
ulp_lp_core_intr_enable
でLPコアの割り込みを有効にすると以下でスタックする。
lp_core_mailbox_send
lp_core_mailbox_send_async
lp_core_mailbox_receive
lp_core_mailbox_receive_async
非同期か同期かによらずスタックする。
送受信もしていないのでHPコアも場合によっては一生待つ。

## MIPI-DSIについて
ESP-IDF5系列と6系列では若干APIが変更されている。
6系列ブランチでは仕様変更に追い付いておらず、また、画面表示に不具合が生じるため、masterブランチの使用が望ましい。

## ESP-ConsoleとUSB CDCについて
ESP-STDIOの出力先にUSB CDCが表示されない。
S3に変更すると表示される。
ESP-STDIOのKconfigに以下があるが、P4を追加してもだめ
depends on (IDF_TARGET_ESP32S2 || IDF_TARGET_ESP32S3)  && !TINY_USB
以下に、P4はUSB関連のコードが存在しない。
components/esp_rom
