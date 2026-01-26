# Neptune_3_Plus

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

## Slicer GCode

### Start GCode

```
;;===== date: 20240520 =====================
M400                                         ; wait for buffer to clear
;[printer_model]
;initial_filament:{filament_type[initial_extruder]}
;curr_bed_type={curr_bed_type}
M220 S100                                    ;Set the feed speed to 100%
M221 S100                                    ;Set the flow rate to 100%
M104 S140                                    ;Set extruder temp to 140C

M190 S[bed_temperature_initial_layer_single] ;Set bed temp and wait
G90                                          ;Use absolute positioning mode
G28                                          ;Home
BED_MESH_CALIBRATE                           ;Generate bed mesh
BED_MESH_PROFILE LOAD=default                ;Load the mesh
G1 Z10 F300                                  ;Move to Z10 at 300 units per min
G1 X{print_bed_max[0]*0.5-50} Y0.5 F6000     ;Move to X center-50, Y0.5 at 6000 units per min
G1 Z0.4 F300                                 ;Move to Z0.4 at 300 units per min
M109 S[nozzle_temperature_initial_layer]     ;Wait for hotend to get to initial layer temp
G92 E0                                       ;Reset Extruder
G1 X{print_bed_max[0]*0.5+50} E30 F400       ;Draw the first line
G1 Z0.6 F120.0                               ;Move to side a little
G1 X{print_bed_max[0]*0.5+47} F3000
G92 E0                                       ;Reset Extruder
;LAYER_COUNT:[total_layer_count]
;LAYER:0
```
