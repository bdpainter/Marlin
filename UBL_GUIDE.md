UBL Setup and Running
=====


*Note: The Z probe on my Prusa is currently unreliable, since it's pulled by the cables and cable chain.  This leads to different readings on the left and right of the bed.  Therefore, you need to follow this guide to set up the UBL grid.*

UBL Grid Creation
------------------

Follow these steps to correctly set up the UBL grid.  This assumes the grid is 5x5; increase or decrease the numbers if using a different size grid.

 0. Set the bed and nozzle temperature to the first-layer values and let them stabilize.
 1. **G28** ; Home, and then verify that the Z offset is correct (the nozzle is at the correct height above the bed at z=0)
 1. **G29 P1** ; This does the auto grid measurement, skipping the outer measurements.  On a 5x5 grid, this will fill in the center 9 points.
 1. **G29 T** ; View the grid.  Ideally it would average near 0, but possibly due to a bug in the bugfix-2.0 branch it averages about -1mm for me.
 1. **G29 P6 C<n\>** ; Use the value at the center of the grid for <n\>.  This will offset the grid by <n\>, making the center value 0.  It's a good start for the manual adjustment.
 1. **G1 X100 Y100** ;  Move the nozzle near the center of the bed.  The manual adjustments will snap to the nearest grid point.
 1. **G29 P4 H0.1 R9** ; Do a manual adjustment for the center 9 points (all the ones that have been defined so far).  The *H0.1* setting tells UBL that your shim (I use a sheet of paper) is 0.1mm thick.  Adjust as necessary.  For each point, use the rotary dial to set the height so that there's friction, but not too much friction, when the shim is between the nozzle and the bed.
 1. **G29 P3** ; Once the center 9 points are set well, use this command to fill in the rest of the UBL grid.
 1. **G29 T** ; Verify that the grid is all filled in.  If it isn't, do additional *G29 P3* until it is.
 1. **G29 P4 H0.1 R25** ; Do the same as the command with *R9*, but this time for all 25 points of the grid.  They should all be pretty close, just needing minor adjustments.
 1. **G29 S** ; Save the Grid
 1. **M500** ; Save the entire setting in EEPROM
 1. **G26 B60 T210** ; Do a test print.


 Running UBL
------------

I use the following startup G code:

```
G21 ;metric values
G90 ;absolute positioning
M82 ;set extruder to absolute mode
M107 ;start with the fan off
G28 ;move to endstops
G29 L # Load from memory
G29 A  # Activate leveling
G29 F10
M109 S[first_layer_temperature_0]
```

UBL is activated with the **G29 L** and **G29 A** commands (the *G29 L* may not be necessary).  The **G29 F10** tells it to fade the leveling out during the first 10 mm.
