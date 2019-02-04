If you want to 3D print:

Either come to one of the 3D printer training sessions listed on the website and emailed to you, or email eceb111lab@gmail.com in order to arrange a different time. If you do not do this, you will not be allowed to 3D print. See the policies page for further details.
Once you have passed the 3D printing training, an account on the Octoprint servers will be created for your team. This will enable you to connect to the printers and print. Please keep this information safe as we don’t want to be constantly resetting passwords. If anything is done to the printers with your account, your team will be held responsible, even if it was not actually any of your team members.
Buy your own filament and start printing. If you have any questions, feel free to ask the lab team. See the quickstart guide for a refresher if you can’t remember all the required steps.

3D printing

TODO: add filament temperature ranges. Probably make into a table

Create your 3D object in a designer of your choice. Recommended software is HERE.
Choose your filament material:
PLA (Polylactic Acid) is a good inexpensive filament for prototypes. It is reasonably strong but very easy to print with and get good results.
ABS (Acrylonitrile Butadiene Styrene) is harder to print with, but is probably the strongest out of the common filaments. It is fairly brittle however. Larger parts have a tendency to warp and lose dimensional accuracy if the part cools down too quickly. Always use the printer enclosures if printing with ABS. ABS can be dissolved with Acetone, which allows parts to be smoothed, as well as plastic welded.
HIPS (High Impact Polystyrene) is slightly easier to print with than ABS and does not warp as much. It has similar strength to ABS. HIPS can be dissolved with Limonene similarly to ABS.
Nylon (Polyamide) is very hard wearing and is ideal for parts that rub or wear such as gears.
There are other specialty filaments such as wood tone, conductive, flexible, etc. If you have unique requirements, ask one of the B111 team members or look around on the internet.
Export your object as a STL file.
Import your file into CURA, which is on the B111 lab computers
Make sure to set up CURA for the appropriate 3D printer, either the Lulzbot Mini or the Lulzbot TAZ 6. You will probably need to do this when you first use CURA on a lab computer.
If you need to switch machine types, you can select from XXX dropdown in CURA.
Under basic settings, select your filament type.
Switch to advanced settings and select your infill, support and other settings. A 20% to 40% infill is good enough for most projects. If you have any questions, feel free to ask the B111 team members. The support options allow for larger overhangs and holes to be printed. If you don’t know exactly what a setting does, then mess around with it in CURA. The only settings you should not touch if you don’t know exactly what you are doing, are the extrude rate and extruder size. TODO: Confirm that these are the right settings.
Position your part on the build platform and choose the scale and rotation of your part. Use your best judgement about the best way to locate and orient your part on the build platform. Some tips:
There either needs to be a flat side on your part, or supports must be on, at least from part to build platform.
The 3D printed parts are often strongest in the horizontal (XY plane), with the vertical axis weaker due to the layering effect.
Circles and holes print much more accurately if printed with their center axis vertical.
Once you are satisfied with how your part looks in CURA, export your part to GCODE. This will contain every instruction to control the 3D printer, from the temperature information to the axis zeroing and cleaning instructions.
Import your part into Octoprint. Select the printer you want to use from the list HERE.
Select your part in the list and load it. Make sure it shows up properly in the preview window.
Select the control tab and preheat the extruder to 250ºC to allow ABS to flow.
Feed cleaning filament through the printing head until it comes out clear. You can either manually push the cleaning filament through the extruder, or lock the filament into the auto feeder and then use the move axis command in Octoprint to extrude the filament. Please do not be stingy with the cleaning filament.
Adjust the extruder temperature to the appropriate temperature for your chosen filament
Insert your filament into the extruder and lock it down and position your filament reel on the holder.
Using the controls in Octoprint, extrude filament until you see it change color from the clear of the cleaning filament.
Remove any excess extruded plastic from around the extruder or bed and then press print in Octoprint. The printer will automatically come up to temperature and start printing. Feel free to monitor the print job anywhere on campus, or while connected to the VPN from off campus.
See the policies page of this website for refreshing your knowledge of the removal and printer usage policies which you covered during your mandatory printer training.
