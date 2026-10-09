# NMOS with ESD Pad Protection

Ali Behbehani, VLSI, University of Denver, Fall 2026

Electric VLSI, mocmos, 300 nm scale, 3 metal layers. I put the NMOS from Tutorial 3 on a die with eight pads. I made two versions: one with plain pads and one where every pad has two ESD diodes.

## Schematic

### Diodes

Both diode cells came with the template. nActive_pWell{sch} is a diode with pWell as the anode and nActive as the cathode. pActive_nWell{sch} has pActive as the anode and nWell as the cathode.

<img src="pictures/images/nactive_pwell_sch.png" width="380">

<img src="pictures/images/pactive_nwell_sch.png" width="380">

### Pad with ESD

pad_esd{sch} is the pad from Lab 2 with two diodes on its net:

- pActive-nWell goes from the pad to vdd.
- pWell-nActive goes from gnd to the pad.

When the pad is between 0 V and VDD, both diodes are off. If the pad goes above VDD, the top diode turns on and sends the current to VDD. If it goes below GND, the bottom diode turns on and pulls current from GND. This keeps the NMOS gate close to the supply range so the gate oxide doesn't break.

pad_esd{ic} has inout on the left, vdd on top and gnd on the bottom.

<img src="pictures/images/pad_esd_sch.png" width="460">

<img src="pictures/images/pad_esd_ic.png" width="300">

### How many pads

The NMOS has four pins: drain, gate, source and bulk. The diodes also need VDD and GND.

Bulk shares the GND pad. The NMOS body is the p-substrate, and the diodes already tie the substrate to GND, so a separate bulk pad would be the same net anyway.

That makes five pads: D, G, S, VDD and GND. A square needs the same number of pads on each side. One per side is four, not enough. Two per side is eight. So eight pads with three spares.

### Padframe

padframe_esd{sch} is the pad_esd icon placed as an array, pad_esd[1:8], with one bus to the export pin[1:8]. vdd and gnd are single wires, so they connect to all eight pads. This is the shared VDD and GND bus. padframe_esd{ic} has the pin[1:8] bus on the left, vdd on top and gnd on the bottom.

<img src="pictures/images/padframe_esd_sch.png" width="460">

<img src="pictures/images/padframe_esd_ic.png" width="300">

### Top level

final_ic_esd{sch} has the NMOS icon and the padframe_esd icon. Like Lab 2, I connect a pin to a pad by naming the wire after the bus member. The vdd wire is named pin[2] and the gnd wire is named pin[7]. The bulk wire is also pin[7], so bulk is on GND. Pads are numbered counterclockwise from the upper right.

| Pad | Location | Connected to |
|---|---|---|
| pin[1] | right, upper | drain |
| pin[2] | top, right | VDD |
| pin[3] | top, left | spare |
| pin[4] | left, upper | gate |
| pin[5] | left, lower | spare |
| pin[6] | bottom, left | spare |
| pin[7] | bottom, right | GND and bulk |
| pin[8] | right, lower | source |

<img src="pictures/images/final_ic_esd_sch.png" width="460">

final_ic_no_esd{sch} is the same thing on the plain Lab 2 padframe, without VDD.

<img src="pictures/images/final_ic_no_esd_sch.png" width="460">

## Layout

### Diodes

From the template. nActive_pWell{lay} is a 15 µm n+ contact above a 15 µm p-well contact. pActive_nWell{lay} is a 15 µm p+ contact with an n-well contact above it, inside an n-well. The contacts are big so the diodes can handle the ESD current.

<img src="pictures/images/nactive_pwell_lay.png" width="300">

<img src="pictures/images/pactive_nwell_lay.png" width="300">

### Pad with ESD

pad_esd{lay} has the Lab 2 pad on top and everything else below it, toward the core.

- The two diodes sit side by side under the pad. I rotated pActive_nWell 180° so both diodes have their pad side on top.
- A Metal-1 strap joins the top of both diodes. A via in the middle connects it to the Metal-2 line from the pad.
- Under the diodes are two Metal-1 rails, 6 µm wide and as wide as the pad cell: vdd on the outside, gnd on the inside.
- The top diode connects straight down to vdd. The bottom diode has to cross vdd to reach gnd, so it jumps over on Metal-2.
- The inout line keeps going on Metal-2 past both rails. Its end is the export the core connects to.

Metal-2 can cross the Metal-1 rails without shorting, which is why the signal and the jumper are on Metal-2.

<img src="pictures/images/pad_esd_lay.png" width="380">

### Padframe

padframe_esd{lay} is eight pad_esd cells, two per side, on a 660 µm square die. Each pad is rotated so its diodes face the center. The vdd rails line up into one ring and the gnd rails into a second ring just inside it. Every pad is rotated the same way, so vdd is always the outer ring and the rings never cross.

The die is bigger than in Lab 2 because the pads now go 204 µm into the die. I kept the pads near the middle of each side so the pads on one side don't run into the pads on the next side at the corners.

<img src="pictures/images/padframe_esd_lay.png" width="460">

### Top level

final_ic_esd{lay} has the NMOS in the middle. Each pin goes out on Metal-1, up through a via, and on Metal-2 to its pad, same as Lab 2.

Pad 2's line already crosses the vdd ring and pad 7's line crosses the gnd ring, so I put a via at each crossing. That makes pad 2 VDD and pad 7 GND.

<img src="pictures/images/final_ic_esd_lay.png" width="460">

Close-up of the NMOS in the middle of the die, with its four Metal-1 / Metal-2 connections going out to pins 1, 4, 7 and 8.

<img src="pictures/images/nmos_closeup.png" width="420">

Zooming from the full die into the NMOS:

<img src="pictures/images/zoom_nmos.gif" width="460">

final_ic_no_esd{lay} is the same NMOS on the plain Lab 2 padframe.

<img src="pictures/images/final_ic_no_esd_lay.png" width="460">

## 3D view

Window > 3D Window > View in 3D. These help show the layers stacked on top of each other: the pad metal at the top, the Metal-2 lines running over the Metal-1 rails, and the diodes under the metal.

pad_esd: the pad, the Metal-2 line going down past both rails, and the two diodes.

<img src="pictures/images/pad_esd_3d.png" width="340">

One of the diodes. Both diode cells look the same in 3D, since the view shows the shapes of the layers and not the doping.

<img src="pictures/images/diode_3d.png" width="200">

padframe_esd and final_ic_esd:

<img src="pictures/images/padframe_esd_3d.png" width="420">

<img src="pictures/images/final_ic_esd_3d.png" width="420">

## DRC

Tools > DRC > Check Hierarchically on final_ic_esd{lay}: 0 errors, 0 warnings. Checking the top cell also checks every cell inside it: NMOS_IV, both diodes, pad, pad_esd and padframe_esd.

<img src="pictures/images/drc_esd.png" width="420">

Same check on final_ic_no_esd{lay}: 0 errors, 0 warnings.

<img src="pictures/images/drc_no_esd.png" width="420">

I also ran it on each diode by itself: 0 errors, 0 warnings for both.

<img src="pictures/images/drc_nactive_pwell.png" width="420">

<img src="pictures/images/drc_pactive_nwell.png" width="420">

## NCC

Tools > NCC > Schematic and Layout Views of Cell in Current Window on final_ic_esd{lay}: the schematic and layout match. This checks that every connection in the layout is the same as in the schematic.

<img src="pictures/images/ncc.png" width="420">

## Files

| File | Contents |
|---|---|
| lab3done/lab_3_nmos_esd_final.jelib | NMOS_IV, nActive_pWell, pActive_nWell, pad, pad_esd, padframe, padframe_esd, final_ic_no_esd, final_ic_esd |
| lab3done/C5_models.txt | Transistor models for the NMOS_IV spice card |
| pictures/images/ | Screenshots |

The NMOS_IV spice card includes C5_models.txt by name, so both files have to sit in the same folder. The library uses 300 nm, 3 metal layers and analog mode. When Electric asks about project preferences on opening it, pick Use All New Settings.

