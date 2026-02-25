# ESP-IDF 6 Compatibility and US915 Join Fixes

This fork includes targeted changes to make `lmic-esp-idf` work reliably with ESP-IDF 6 and US915 OTAA networks.

## 1) Build / Component updates
- `CMakeLists.txt`
  - Added `src` to include paths.
  - Added explicit component requirements: `esp_driver_gpio`, `esp_driver_spi`, `esp_timer`.
  - Added `-Wno-error=format` for LMIC printf format warnings under stricter toolchains.

## 2) HAL updates for IDF 6
- `src/hal/hal.c`
  - Replaced legacy timer-group usage with `esp_timer` in `hal_ticks()`.
  - Updated GPIO interrupt enum usage (`GPIO_INTR_DISABLE`) and pin masks to 64-bit style (`1ULL << pin`).
  - Added a safe fallback SPI host define when `LMIC_SPI` is not set.

## 3) SPI host naming compatibility (IDF 5 vs IDF 6)
- `src/lmic/config.h`
  - Added compatibility mapping for host names:
    - IDF 5.x style: `HSPI_HOST` / `VSPI_HOST`
    - IDF 6.x style: `SPI2_HOST` / `SPI3_HOST`
  - Added fallback host selection logic and comments documenting this behavior.

## 4) US915 join behavior improvements
- `src/lmic/lmic.c`
  - `nextJoinState()` now respects enabled channel masks for both 125 kHz and 500 kHz join channels.
  - US915 JoinAccept handling now accepts extended JoinAccept payloads and ignores CFList bytes instead of rejecting the frame.

## 5) Minor cleanup
- `src/lmic/radio.c`
  - Removed a verbose IRQ debug print block that could trigger format-type warnings.

## Notes
- The US915 extended JoinAccept handling change is critical for networks that send 33-byte JoinAccept payloads.
- These patches were validated with ESP32 + SX1276 (RFM95W) and ChirpStack US915 OTAA.
