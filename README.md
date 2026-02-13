# VABT-Mode in NUCLEO-G0B1RE


VBAT is the dedicated power supply for the backup domain of the STM32G0B1, which includes:

- RTC (Real-Time Clock)
- TAMP (Tamper detection)
- LSE (Low-Speed External 32.768 kHz oscillator)
- Backup registers

The VBAT domain remains powered when VDD is not present, allowing timekeeping and backup data retention.

VBAT is supplied externally through the **VBAT pin**.

## What happens when VDD is removed?
When VDD is removed:
* The MCU enters reset.
* The core and SRAM are powered down.
* Peripheral configuration is lost

However, if a backup source is connected (VBAT):
* The RTC is actived.
* Backup registes remain preserved.
* TAMP logic remains active.
* LSE oscillator keeps running.

This ensures that time and backup data are retained even when the main supply is disconnected.


## VBAT Backup Option
The VBAT domain typically uses:
* A coin cell battery
* A supercapacitor

## Charging the VBAT-Pin:

The STM32G0B1 includes an internal charging path between **VDD** and **VBAT**, configurable via software.


Use `HAL_PWREx_EnableBatteryCharging(uint32_t ResistorSelection)` to enable charging the Battery or `HAL_PWREx_DisableBatteryCharging()` to disable.

**ResistorSelection** parameter allows choosing between two internal resistors:
* PWR_BATTERY_CHARGING_RESISTOR_5:  5 kΩ
* PWR_BATTERY_CHARGING_RESISTOR_1_5:  1.5 kΩ

This resistor is internally connected between VDD and VBAT, allowing the VBAT source (battery or supercapacitor) to charge when VDD is present.

## Current Consumption
<center>

| **Current Consumption**| **Measured Current<br>Consumption** |
| :--------------------- | :--------------------------------|
| 247.5 [nA]             | 400 [nA]                         |

</center>
<br>

> This data is interpolated between VDD = 3.6 V and VDD = 3.0 V to obtain values at VDD = 3.3 V and RTC is enabled. <br>
> You can consult the theory current consumption in [STM32G0B1 Datasheet](https://www.st.com/resource/en/datasheet/stm32g0b1cc.pdf) in the section **5.3.5 Supply current characteristics** to review your particular case.

## How to test VBAT mode?

1. Power off the board.

2. Remove SB26.

3. Connect 3.3 V directly to the VBAT pin, or connect a supercapacitor to VBAT.

4. Run the program on the NUCLEO-G0B1RE and verify the RTC date and time using a serial terminal.

5. Disconnect JP3 to remove the VDD supply. The VBAT source will now power the backup domain.

6. Reconnect JP3 and verify that the date and time were preserved.

> You can consult [Nucleo-G0B1RE schematic](https://www.st.com/resource/en/schematic_pack/mb1360-g0b1re-c02_schematic.pdf).

### Serial Configuration Parameters

If you want to see the Date and Time of the RTC, configure the serial terminal with the following settings:

* Port: COMx (check Device Manager)
* Baud rate: 115200
* Data bits: 8
* Parity: None
* Stop bits: 1
* Flow control: None

## References

- STMicroelectronics. (2024). *RM0444: STM32G0x1 advanced Arm®-based 32-bit MCUs reference manual* (Rev. 6).  
  https://www.st.com/resource/en/reference_manual/rm0444-stm32g0x1-advanced-armbased-32bit-mcus-stmicroelectronics.pdf

- STMicroelectronics. (2024). *STM32G0B1xB/xC/xE Arm® Cortex®-M0+ 32-bit MCU datasheet* (DS13560 Rev. 5).  
  https://www.st.com/resource/en/datasheet/stm32g0b1cc.pdf

- STMicroelectronics. (2020). MB1360-G0B1RE-C02 board schematic (Schematic Pack) [PDF]. STMicroelectronics. https://www.st.com/resource/en/schematic_pack/mb1360-g0b1re-c02_schematic.pdf
