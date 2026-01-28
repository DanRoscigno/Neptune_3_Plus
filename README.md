# Neptune_3_Plus

## PrusaSlicer GCode

### Start GCode

```
G90                                    ;Use absolute coordinates
M220 S100                              ;Reset Feedrate
M221 S100                              ;Reset Flowrate
M104 S{first_layer_temperature[0]-60}  ;Set temporary nozzle temp to prevent oozing during homing
M190 S{first_layer_bed_temperature[0]} ;Wait for bed temp to stabilize
M109 S{first_layer_temperature[0]-60}  ;Set temporary nozzle temp to prevent oozing during homing
G28                                    ;Home all
BED_MESH_CALIBRATE                     ;Generate a bed mesh (probes 49 points on bed
BED_MESH_PROFILE LOAD=default          ;Load the bed mesh that was just probed
G1 Z10 F240
G1 X2 Y10 F3000
M109 S{first_layer_temperature[0]}     ;Wait for nozzle temp to stabilize
G92 E0                                 ;Reset Extruder
G1 Z0.45 F300
G1 X1.5 Y20 F5000.0                    ;Move to start position
G1 Y120.0 F600.0 E15                   ;Draw the first line
G1 X0.5 F1000.0                        ;Move to side a little
G1 Y20 F600 E30                        ;Draw the second line
G92 E0                                 ;Reset Extruder
```

### End GCode

```
M104 S0 ; turn off heaters
G28 X0  ; home X axis
M84     ; disable motors
```

## Klipper links

[Reddit](https://www.reddit.com/r/ElegooNeptune3/comments/1mbyt2n/neptune_3_proplusmax_printers_running_klipper_can/?share_id=pa4ONSfTpuDBc9Xl_lwk-&utm_content=2&utm_medium=android_app&utm_name=androidcss&utm_source=share&utm_term=1)

[Violet's Building firmware](https://pages.tralce.com/3-d-printing-guides/setting-up-klipper-on-my-neptune-3-pro/)

[Violet's configs](https://github.com/tralce/klipper-configs-public)

[Display](https://www.amazon.com/ELECROW-Display-1024X600-Function-Raspberry/dp/B01GDMDFZA/)

[Pro Config from the Klipper repo](https://raw.githubusercontent.com/Klipper3d/klipper/refs/heads/master/config/printer-elegoo-neptune3-pro-2023.cfg)

[Feral Engineer](https://raw.githubusercontent.com/TheFeralEngineer/Klipper-for-Elegoo-Neptune-series-3D-Printers/refs/heads/main/Neptune%203%20Pro%2C%20Plus%2C%20Max/Neptune%203%20Plus/printer.cfg)

[Benjamin Reich's backup](https://github.com/reib3n/Klipper-Neptune3Plus/blob/main/printer_data/config/printer.cfg)

[Ricky Impey's videos](https://youtube.com/playlist?list=PLC4bOo0vesmLJ211bmSiWaJ9ahBUQUjjv)

[Discourse](https://klipper.discourse.group/)

## My notes

To configure the `[mcu]` section you need to know the serial device ID. I _think_ that I can get that by ssh'ing into the Pi and `ls /dev/serial/by-id/*`. If that is not it then watch this [video](https://www.youtube.com/watch?v=W3i1Aa2mRJE)
