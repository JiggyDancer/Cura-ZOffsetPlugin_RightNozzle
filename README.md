# RightNozzle Fork

Calibrating the Z Offset on my Ultimaker 3 is such a pain.  The process relies on wiggling a card under the nozzle to "measure" the distance from the build plate.  There is no precision in the process and the settings produced are saved in FIRMWARE and there is no way to fine tune the settings aftewards.  Sure you can use the thumb screws to adjust the build plate, but there is no way to adjust the offset of the right nozzle independently.  This fork of Cura-ZOffsetPlugin attempts to add a setting that allows for the individual adjustment of the right nozzle.  These scripts make adjustments to the Z height in the gcode on a per nozzle basis when slicing with Cura, so unfortunately it will not have any effect on XY calibration.

Replace ZOffsetPlugin.py in :
_C:\Users\<UserName>\AppData\Roaming\cura\<CurrentVersion>\plugins\ZOffsetPlugin\ZOffsetPlugin_



# ZOffsetPlugin

This plugin adds a setting named "Z Offset" to the Build Plate Adhesion settings in the Custom print setup of Cura. Entering a positive value to in field will result in the head printing everything that amount further away from the build plate.

The Z Offset setting can be found in the Custom print setup by using the Search field on top of the settings. If you want to make the setting permanently visible in the sidebar, right click and select "Keep this setting visible".

The plugin adjusts the first move on the Z axis in the first layer by adding the Z offset value, and then instructs the printer to consider this the original first layer height. For example, layer 1 of a print with an Initial Layer Height of 0.25, and a Z Offset value of 0.05 may look like this:
```
...
;LAYER:0
M107
G0 F4320 X135.625 Y125.625 Z0.3 ;adjusted by z offset
G92 Z0.25 ;consider this the original z before offset
;TYPE:WALL-INNER
...
```

Unfortunately this technique does not work for Ultimaker printers with firmware 5.2 or newer due to a bug in the firmware for those models. For those printers, the whole gcode file is processed to offset all moves that include a Z coordinate. This takes longer, but is otherwise functionally equivalent. Since some other printers also misbehave after using the G92 command, this alternative "extensive processing" method can now be selected as an additional setting for all printers.

With either method, the Z Offset can never be lower than the negative of the Initial Layer Height. That would result in a negative Z value, which at best would be caught by the firmware and result in an error during printing, and at worst would lead to the nozzle being pushed into the build plate, damaging the printer. So with an Initial Layer Height of 0.25, the Z Offset is limited to be -0.25 or higher.
