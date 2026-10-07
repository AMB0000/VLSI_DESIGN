# DAC on a Padframe

Ali Behbehani, VLSI, University of Denver, Fall 2026

Electric VLSI, mocmos, λ = 300 nm, 3 metal layers. I took the R-2R converter from Lab 1 and gave it a way off the chip: eight bonding pads in a square ring with the converter in the middle.

## Schematic

### Pad

pad{sch} is one Off-Page connector exported as inout. Nothing else, since this is a bare bonding pad with no ESD diodes or drivers. It only needs a single net so it can be netlisted against the layout. pad{ic} is a box with that inout port.

<img src="pictures/pad_sch.png" width="420">

### DAC

Reused from Lab 1 unchanged. Five r_divider slices stacked from b4 down to b0, ending in one 10 kΩ n-well resistor to ground that completes the 2R termination.

The ladder is purely resistive, so the bits drive it directly and there is no VDD. Seven signals have to leave the die: b0 to b4, vout, and gnd.

<img src="pictures/dac_sch.png" width="280">

### How many pads

Seven signals needs seven pads minimum, and a square ring needs the same count on every side. One per side gives four, not enough. Two per side gives eight, the smallest square that fits seven. So eight pads, one left as a spare.

### Padframe

padframe{sch} is the pad icon placed once as an array, pad[1:8], tied with one bus to the export pin[1:8]. The array saves drawing eight pads and eight separate wires.

<img src="pictures/padframe_sch.png" width="420">

### Top level

ic{sch} puts the dac icon next to the padframe icon. I connect a DAC pin to a pad by naming the wire after the bus member, so a wire named pin[5] on vout joins pad 5. Pads are numbered counterclockwise from the upper right.

| Pad | Location | Connected to |
|---|---|---|
| pin[1] | right, upper | b4 |
| pin[2] | top, right | gnd |
| pin[3] | top, left | spare |
| pin[4] | left, upper | b0 |
| pin[5] | left, lower | vout |
| pin[6] | bottom, left | b1 |
| pin[7] | bottom, right | b2 |
| pin[8] | right, lower | b3 |

<img src="pictures/ic_sch.png" width="460">

## Layout

### Pad

pad{lay} is three layers on the same center: a 400λ artwork box for the boundary, a 244λ Metal-2 / Metal-3 contact where the bond wire lands, and a 200λ passivation opening. The metal is 22λ bigger than the hole on each side so the edge of the opening never falls on bare oxide.

<img src="pictures/pad_lay.png" width="300">

### Padframe

padframe{lay} is the eight pads, two to a side, around a 1600λ square. Pads are 400λ wide and 400λ apart, so the two on each side touch edge to edge. The corners stay empty and the middle is open for the converter.

<img src="pictures/padframe_lay.png" width="420">

### Top level

ic{lay} has the converter in the cavity, turned 90°. Each connection starts on Metal-1 at the DAC pin, goes up through a Metal-1 / Metal-2 contact, and finishes on Metal-2 into the pad, since the pad is Metal-2 / Metal-3 and has no Metal-1 to land on.

The padframe instance covers the whole die, so I unchecked Easy to Select on it. Otherwise every click inside the ring grabs the frame instead of the converter or a wire.

<img src="pictures/ic_lay.png" width="460">

## DRC

Tools > DRC > Check Hierarchically on ic{lay}: 0 errors, 0 warnings.

<img src="pictures/drc.png" width="420">

## Files

| File | Contents |
|---|---|
| lab2_padframe.jelib | pad, padframe, ic, dac, r_divider, r_10k |
| lab1.jelib | Lab 1 library, dac{lay} pulls r_10k from it |
| pictures/ | Screenshots |

lab2_padframe.jelib links to lab1.jelib by name, so both have to sit in the same folder and the Lab 1 file has to be called exactly lab1.jelib.
