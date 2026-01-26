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

### End GCode

```
;PRINT_END
G90                                                                                            ;Absolute positionning
M83                                                                                            ; extruder relative mode
G1 X10 Y{print_bed_max[1]*0.8} Z{min(max_layer_z+50, printable_height)} E-5 F{travel_speed*60} ; Move print head up
M106 S0                                                                                        ;Turn-off fan
M104 S0                                                                                        ;Turn-off hotend
M140 S0                                                                                        ;Turn-off bed
M84 X Y E                                                                                      ;Disable all steppers but Z
```

### Before layer change GCode

```
;BEFORE_LAYER_CHANGE
;[layer_z]
G92 E0  ;Reset the extruder before the next layer
```

## Orca Klipper settings for Neptune 3 Plus

### Start GCode
```
M413 S0 ; disable Power Loss Recovery
G90 ; use absolute coordinates
M83 ; extruder relative mode
M104 S120 ; set temporary nozzle temp to prevent oozing during homing and auto bed leveling
M140 S[bed_temperature_initial_layer_single] ; set final bed temp
G4 S10 ; allow partial nozzle warmup
G28 ; home all axis
G29 ; run abl mesh
M420 S1 ; load mesh
G1 Z50 F240
G1 X2 Y10 F3000
M104 S[nozzle_temperature_initial_layer] ; set final nozzle temp
M190 S[bed_temperature_initial_layer_single] ; wait for bed temp to stabilize
M109 S[nozzle_temperature_initial_layer] ; wait for nozzle temp to stabilize
G1 Z0.28 F240
G92 E0
G1 Y140 E10 F1500 ; prime the nozzle
G1 X2.3 F5000
G92 E0
G1 Y10 E10 F1200 ; prime the nozzle
G92 E0
```

### End GCode

```
{if max_layer_z < printable_height}G1 Z{z_offset+min(max_layer_z+2, printable_height)} F600 ; Move print head up{endif}
G1 X5 Y{print_bed_max[1]*0.8} F{travel_speed*60} ; present print
{if max_layer_z < printable_height-10}G1 Z{z_offset+min(max_layer_z+70, printable_height-10)} F600 ; Move print head further up{endif}
{if max_layer_z < printable_height*0.6}G1 Z{printable_height*0.6} F600 ; Move print head further up{endif}
M140 S0 ; turn off heatbed
M104 S0 ; turn off temperature
M107 ; turn off fan
M84 X Y E ; disable motors
```
