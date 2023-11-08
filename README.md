# brose_info

I try to group all info about the can/lin/bt for the brose motor

Some ressources i found

## A old service tool
https://archive.org/details/BMZServiceToolSOFTWAREBROSE

Thare a few intersting files in there

Two libraries python (it's pyc, but easy to decompile, you still have comment and everythong in file, i don't push code here as it's not open source)

An hex file with the boot loader and the firmware for the HMI (old one, not the Allround)

The exe file (py2exe, i need to decompile it yet) that update the firmware on the HMI

The exe file that convert the hex file to dfu

Brose seems tu use LIN over CAN to do the update of firmware

## Bluetooth ios sdk

https://github.com/grandcentrix/brose-ios-sdk/releases

## Someone has decoded soms can message from the battery
https://www.pedelecforum.de/forum/index.php?threads/analyse-ladevorgang-und-akku-can-nachrichten-am-levo-2019.60168/

https://www.pedelecforum.de/forum/index.php?threads/turbo-levo-can-analyzis-parameter-setting-module.52935/page-4#post-1296977

## What i've found

### Bluetooth

Bluetooth seems to use protobuffer

proto definition (extract from the brose apk) can be found in bluetooth folder

### CAN / LIN
from the bloks_usb.py file in the old servicetool

HMI with usb support at least those commands :

- enter_testmode = 'enter_testmode'
- activate_lin_bridge = 'lin_bridge'
- help = '?'
- info = 'info'
- reset_bl = 'reset_bl'
- start_application = 'start_application'
- pin_usb_id = 'usb_id'
- pin_over_i = 'usb_overi'
- pin_vbus_en = 'vbus_en'
- pin_can_s = 'can_s'
- pin_dcdc_en = 'dcdc_en'
- pin_lcd_bias_en = 'lcd_bias_en'
- pin_lcd_en = 'lcd_en'
- pin_flash_en = 'flash_en'
- pin_inh = 'lin_inh'
- pin_wake = 'wake'
- pin_slp_n = 'slp_n'
- pin_bak_l_en = 'bak_l_en'
- pin_testpin = 'testpin'
- PinMap = 
    - PIN_usb_id: 1, 
    - PIN_over_i: 2, 
    - PIN_vbus_en: 3, 
    - PIN_can_s: 4, 
    - PIN_inh: 5, 
    - PIN_wake: 6, 
    - PIN_slp_n: 7, 
    - PIN_dcdc_en: 8, 
    - PIN_bak_l_en: 9, 
    - PIN_lcd_en: 10, 
    - PIN_lcd_bias_en: 11, 
    - PIN_flash_en: 12
- pwm_set = 'pwm_set'
- cmd_send_can = 'can_write'
- cmd_read_can = 'can_read'
- cmd_send_lin = 'lin_write'
- cmd_read_lin = 'lin_read'
- test_pattern = 'lcd_testpattern'
- light_sensor = 'light_sensor'
- lcd_set_map_byte = 'lcd_patternline'
- lcd_setsegment = 'lcd_setsegment'
- read_eeprom_val = 'read_eeprom_val'
- write_eeprom_val = 'write_eeprom_val'

- CMD_PARAMETER_DELIMITER = ' ' (a space)
- PIN_high = CMD_PARAMETER_DELIMITER + 'high'
- PIN_low = CMD_PARAMETER_DELIMITER + 'low'
- LCD_TEST_PATTERN_ALL_ON = 255
- LCD_TEST_PATTERN_ALL_OFF = 0
- LCD_TEST_PATTERN1 = 240
- LCD_TEST_PATTERN2 = 15

#Enable/disable lin bridge over usb
```
data = activate_lin_bridge + CMD_PARAMETER_DELIMITER + '1' (or 0 to deactivate)
```
#send a command over usb lin bridge
```
data = cmd_send_lin + command
```
#read lin
```
data = cmd_read_lin + CMD_PARAMETER_DELIMITER + ($LENGTH)
```

send and receive on can is the same (but with command cmd_send_can/cmd_read_can)

There is no code that use it, and no code to initlaise the can bridge, so either it's missing, either you don't need to activate it

The servicetool can be decompiled too in c sharp, but there's no data in it, everything is donwloaded at runtime

Maybe some finding about specialized to calculate a CRC on CAN message (no read all the code, but it seem specific to specialized version)

### next step
- Connect on the can bus, i'm waiting for can board, and have to find the hmi connector pinout

- Decode CAN message

    For that, i will probably try to connect in bluetooth, log data in influxdb
    log can message in influxdb
    use grafana to look for corelation between can message and BT info


