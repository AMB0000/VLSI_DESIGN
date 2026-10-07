# DAC on a Padframe

Ali Behbehani VLSI, University of Denver, Fall 2026

Electric VLSI, mocmos technology, λ = 300 nm, 3 metal layers. In this lab I take the R-2R converter from Lab 1 and give it a way off the chip: eight bonding pads in a square ring with the converter sitting in the middle.

## Schematic

### Pad cell

I kept the pad as simple as possible. pad{sch} holds one Off-Page connector exported as inout, plus the pad icon. Nothing else is in it, since this is a bare bonding pad with no ESD diodes or drivers. All the schematic needs to do is give the pad a single net so it can be netlisted and compared with the layout.

The icon, pad{ic}, is a box with the inout port on its left edge.

![pad schematic](images/pad_sch.png)

*pad{sch} with pad{ic} below it*

### DAC

I reused the Lab 1 converter unchanged, so the full design and simulation write-up is in that report. In short: five r_divider slices are stacked from b4 at the top to b0 at the bottom, and the bottom slice ends in one 10 kΩ n-well resistor (W = 14λ, L = 164λ) to ground, which completes the 2R termination. Each slice is a 2R leg (two 10 kΩ in series) from its bit input to the ladder node and one R down to the next slice.

Because the ladder is purely resistive, the bit inputs drive it directly and it has no VDD connection. That leaves seven signals that have to leave the die: the inputs b0 to b4 (b4 is the MSB), the output vout, and gnd. All seven are exported on dac{sch}, dac{ic} and dac{lay}.

![DAC schematic](images/dac_sch.png)

*dac{sch}, the five stacked slices and the termination resistor, with dac{ic} at the top left*

### How many pads

Seven signals means at least seven pads. The lab also wants the frame square, and a square ring only works if every side carries the same number of pads. With one pad per side I only get four. With two per side I get eight, which is the smallest square that fits seven, so I used eight and left one as a spare.

### Padframe

In padframe{sch} I placed the pad icon once as an 8-wide array, pad[1:8], and tied it with one bus to the export pin[1:8]. An array saves me from drawing eight separate pads and eight separate wires. The padframe icon has that single bus port.

![padframe schematic](images/padframe_sch.png)

*padframe{sch}, the pad array on one bus, with padframe{ic} above it*

### Top level

ic{sch} puts the dac icon next to the padframe icon. To connect a DAC pin to a pad I named the wire after the bus member it belongs to, so a wire named pin[5] on vout joins pad 5. I numbered the pads counterclockwise starting from the upper right one:

| Pad | Location | Connected to |
|---|---|---|
| pin[1] | right side, upper | b4 |
| pin[2] | top, right | gnd |
| pin[3] | top, left | nothing (spare) |
| pin[4] | left side, upper | b0 |
| pin[5] | left side, lower | vout |
| pin[6] | bottom, left | b1 |
| pin[7] | bottom, right | b2 |
| pin[8] | right side, lower | b3 |

![top level schematic](images/ic_sch.png)

*ic{sch}. The named wires carry the connection, so there is no drawn arc between the converter and the bus*

## Layout

### Pad cell

pad{lay} is three layers centered on the same point. The outer artwork box is 400λ (120 µm) on a side and marks the pad boundary. Inside it is a 244λ Metal-2 / Metal-3 contact, which is the metal the bond wire actually lands on. On top of that is a 200λ (60 µm) passivation node, the hole in the overglass. I made the metal 22λ bigger than the hole on each side so the edge of the opening never falls on bare oxide. The inout export sits on the contact.

![pad layout](images/pad_lay.png)

*pad{lay}. The via array ties Metal-2 and Metal-3 together across the whole pad, and the passivation opening sits inside the metal on all four sides*

### Padframe

padframe{lay} has the eight pads arranged two to a side around a 1600λ square. Since the pads are 400λ wide and spaced 400λ apart, the two pads on each side touch edge to edge. The corners stay empty and the middle is open, which is where the converter goes. The exports pin[1] to pin[8] follow the same counterclockwise order as the table above, so they line up with the icon.

![padframe layout](images/padframe_lay.png)

*padframe{lay}, eight pads two to a side with the cavity open in the middle*

### Top level

In ic{lay} I placed the padframe and the converter layout, with the converter turned 90° to fit the cavity. To wire a DAC pin out I start on Metal-1 (5λ wide) at the pin, go up through a Metal-1 / Metal-2 contact, and finish on Metal-2 (13λ wide) into the pad. The jump to Metal-2 is needed because the pad is made of Metal-2 and Metal-3 and has no Metal-1 to land on.

The padframe instance covers the whole die, so with normal selection every click inside the ring would grab it instead of the converter or a wire. I unchecked Easy to Select on it, and when I need to touch the padframe itself I switch to special select.

![top level layout](images/ic_lay.png)

*ic{lay}. Seven pads wired, pin[3] at the top left left bare. Metal-1 in blue runs from the ladder out to a contact at each pad, then Metal-2 in magenta finishes into the pad*

## DRC

I ran Tools > DRC > Check Hierarchically on ic{lay} with the mocmos rules, area and extension bits on. The check walked the hierarchy down into dac{lay} and reported 0 errors and 0 warnings across 9 networks.

![DRC result](images/drc.png)

*Messages window after the hierarchical DRC run on ic{lay}*

## Files

| File | Contents |
|---|---|
| lab2_padframe.jelib | pad, padframe, ic, and the converter cells dac, r_divider, r_10k |
| lab1.jelib | Lab 1 library; dac{lay} pulls r_10k from it |
| images/ | Screenshots used above |

lab2_padframe.jelib links to lab1.jelib by name, so both need to be in the same folder and the Lab 1 file has to be called exactly lab1.jelib.
