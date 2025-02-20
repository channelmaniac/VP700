# VP700
RCA VP700 Tiny BASIC Card

This was a project by the late Ed Keefe. He had dumped the VP700 Tiny BASIC card from RCA for the COSMAC VIP computer, reverse engineered the schematic from the board, and built a PCB to try and reproduce the card. The original can be seen on his web site at: https://www.cosmacvip.com/GALLERY/LARGEWM/IMG_2589.jpg

I was given a copy of the gerbers and had some PCBs made but could not get his PCB working. The problem boiled down to timing, address decoding, and which signals got used where. I figured out one part of it but was stuck getting it to boot immediately to the card after a reset of the CPU. Walter Miraglia was very helpful in working with me on this he engaged Josh Bensadon who figured out the issues, the proper connections for each part of the signalling, and that a tiny capacitor was needed to stretch a pulse on the /MRD (Memory Read) line.

Based on Josh's changes, I laid out a new card using Ed's logo like his had plus his name, which, additionally, I'd like to extend many thanks to Con Skordis for help with the 44 pin edge connector in KiCAD.

There are some difficulties in designing for this system. The address bus is multiplexed with the TPA signal used to define when the lower address bus information is available then when the upper address bus information is. The 8000 signal is a flipped version of the /8000 used to access the monitor ROM. When the 8000 signal is low, the lower half of memory is addressed. The lower half of memory is RAM and is incompletely decoded for access, resulting in mirrors at every 4K for the lower half of memory. The RAM can be disabled by asserting the MINH signal on the expansion bus, taking it to logic High. The Tiny BASIC card makes use of this.

The Tiny BASIC card represented some challenges as the Mask ROMs from RCA not only latch the address information internally, they also have internal address decoding so no external ICs for decoding addresses were necessary on that card. The 4516 CMOS chip is a 4 to 16 decoder and while it could be used to decode the addresses for the ROMs, it wasn't. It was used to decode the RAM address, disable the RAM from 0000-0FFF, and enable only the bank of RAM from 1000-1FFF using the MINH line. This is because the Tiny BASIC code sits at 0000-0FFF in memory and would otherwise conflict with the RAM. The diode on the MINH signal is because RCA specified a diode to be used on that signal line in documentation. To further the difficulty in understanding how this card works, the upper address lines are not used in order for inputs on the 4516. Instead of mapping the inputs D(A15), C(A14), B(A13), and A(A12) being used, RCA mapped them as D(A15), C(A13), B(A12), and A(A14). This means you must build a table to map the upper address lines to understand the outputs properly.

Lower Memory Map:  (NOTE: the upper half of memory exhibits the same mirroring behavior with the Monitor ROM.)

0000-03FF - Bank 0 RAM (2x 2114 RAM chips)

0400-07FF - Bank 1 RAM (2x 2114 RAM chips)

0800-0BFF - Bank 2 RAM (2x 2114 RAM chips)

0C00-0FFF - Bank 3 RAM (2x 2114 RAM chips)

1000-1FFF - Mirror of bank 0-4

2000-2FFF - Mirror of bank 0-4

3000-3FFF - Mirror of bank 0-4

4000-4FFF - Mirror of bank 0-4

5000-5FFF - Mirror of bank 0-4

6000-6FFF - Mirror of bank 0-4

7000-7FFF - Mirror of bank 0-4

This new card uses the 4516 to also decode the address for the EEPROM / EPROM used to hold the Tiny BASIC code to have it appear in memory from 0000-0FFF so the only additional chip needed is the 74HC573 to latch the upper 8 address lines. The card calls for a single 28C256 but the upper address lines are grounded. You are only using the lower 4K. If a 28C256 can't be found, a simple 27C64 will work just fine.

Thanks to Walter Miraglia for the page scans of the RCA Tiny BASIC manual. I've put them together in a single PDF file and put them here to make things easy.

The Tiny BASIC ROM code is as simple as installing the EMMA 02 emulator and grabbing the VP700_Tiny_BASIC.bin file from it to program into your own IC.

PCBs are on order from JLCPCB. Once verified the files will be placed in this repository.
