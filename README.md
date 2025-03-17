# nTerm2-S
My rendition of the uTerm2-S by Just4Fun (https://hackaday.io/project/181583-uterm2-s-a-multi-emulation-color-rs232-terminal). This one is in SMD, and I hopefully made it easily hand-solderable for everyone (with maybe the exception of the CP2102). Obviously the 'n' in nTerm stands for 'nano.' :P

The nTerm2-S has a slightly more usable form the uTerm2-S, I think. Smaller and a little more usable when sitting permanently on a desk. And as an extra, it can switch between normal and cross (null-modem) from the software so that you wil never have to search for that null-modem cable ever again. It can also switch between RTS/CTS or DTR/DSR handshaking.

Supports RxD/TxD with RTS/CTS. Also supports DTR/DSR in a way. It is possible to switch the RTS/CTS lines to the DTR/DSR lines, using the ESP32's RTS/CTS support to manage the DTR/DSR. It's not really DTR/DSR, but as the ESP32 is a plenty fast enough DTE and only the DCE might be slow, it will work most of the times.

The nTerm2-S is ultimately based on the FABGL library and board design from Fabrizio Di Vittori. http://www.fabglib.org. However, I skipped Fabrizio's implementation and based my design on Just4Fun's uTerm2-S. https://j4f.info/uterm2-s

THIS IS A WORK IN PROGESS! I.E. UNLESS STATED AS WORKING, ASSUME THAT IT DOES NOT WORK! NO FIRMWARE HAS BEEN TESTED YET, AND AS THERE ARE SMALL DIFFERENCES IN GPIO ASSIGNMENTS, I DON'T EXPECT THE DEFAULT FABGL EXAMPLES TO WORK WITHOUT TWEAKS.

<sub><s>NOTE: The relay circuit with the bistable relays is directly out of the OMRON databook, and it allows me to switch the relay but use only power when switching (and some losses through the 470 resistor when switched on: 7mA continuous, which is a huge of a lot better than 30mA continuous when using a normal relay). That circuit sources the power from the input, which is connected to a GPIO. ESP32 GPIO pins can source 40mA and sink 28mA. According to the datasheet, the relay needs 100mW of power to switch. At 3V, that is 33mA, but we use 3.3V, so some 30mA. So when switching 'on', the ESP32 will need to source 30mA and settle at some 7mA (all theoretically, be advised). That is quite a lot, but still within spec. The ESP32 does not have to sink much, as the reverse current will go through the diode directly to ground.</s><sub>

<sub>It turned out that the ESP32 was not able to source enough power after all, so I have had to change the circuit somewhat. Now all the necessary power comes from the 5V Vbus and is switched through another transistor. I couldn't properly measure the current draw from the ESP32's GPIO port, but it looked as if it was some 4mA.</sub>

<sub>Progress! I received my fuses and did some real measurements. I used 3.3V, BAT45 (commit says 1N4148, but I was mistaken, they are BAT45's), BC337, 220u and 470 ohm on my bread board, and measured about 14mA max when switching, and 6.8mA continuous. I expected more than 14mA when switching, but maybe this relay is better than the specs? In any case, I decided to experiment with the C and R. I upped the C to 470uF, which seemed to make the switching more reliable. But more importantly, I increased the resistor in steps to see what the result would be. And found that I could go up to at least 3K9 before the switching would become unreliable. 10K was clearly too much, and I didn't try anything in between 3K9 and 10K. With 3K9, 0.88mA is drawn, and I find that good enough. The low current power led uses 3mA, and I keep that on all the time. 1mA is fine.</sub>

<sub>Created the gerbers for v0.1 and sent out to JLPCB. Jeez, it's cheap. 11 euro for 5 boards. Still had a bunch of warnings from the pins/vias of the USB-C connector and ESP32 footprints that I just could not resolve. Decided to take my chances, and JLCPCB accepted the gerbers and has actually already finished and posted the boards. So I expect the boards to be here in a few days. In the mean time, I've been doing some basic work and redesign for a version 2 of the nTerm2-S, with an SD card slot and psram, to make it more versatile (e.g. PC emulation). However, let's get this first version to work first, the objective is to have a terminal with software switchable null-modem.</sub>

<sub>After my v0.1 boards arrived I built one up. I did it in stages, starting with the CP2102 and then the power (I use the CP2102 in bus-powered mode). After much cursing, I got the CP2012 straight on the pads and it looked good under the microscope. I then added the power supply components and the USB-C port. No ESD protection yet. Doublechecked everything 10 times under the microscope, got out an old cheap USB hub (better to blow up the hub than my Macbook's port) and plugged the nTerm in. And Windows detected the CP2102 immediately, installed the drivers, and... success #1 was there!

I then added the ESP32. Could update its firmware. Success #2! Soldered the video DAC and VGA port (and fixed the ESP32). Success #3: we have video! Then populated the keyboard and mouse circuits. Success #4! After playing some Space Invaders, I populated the MAX3232 and hardwired the Rx and Tx together to do a loopback test. And we had success #5!

Then the problems started. After populating the relay circuits, I could not get them to switch. After lots of measuring and experimenting with an oscilloscope, I could only conclude that the ESP32 was simply not able to source enough power and all my calculations and experiments apparently had been wrong. So now I had to experiment with the board itself and found that all I could do was buffer the ESP32's GPIO with another transistor buffer. I.e. replace the BAT43 diode with a BC817 transistor. After fiddling with resistor values, I got it all working reliably in the end. But it needed a new board revision.

Problems encountered and fixed:

1. The CP2102 was really hard to solder, even using hot air. I am looking into the FT231 as a much easier to solder alternative. But the FT231 seems to have more driver issues than the CP2102 (which has none, as far as I know). As an alternative, it might help to increase the size of the pads.
2. The ESD protection diodes were much tinier than I thought. Looking at the PCB in Kicad, they didn't seem so small. :) So I should try to find larger diodes.
3. I thought I had an ESP32 lying around, but it turned out that I only had one that was soldered to a breakout board. So I decided to desolder that, instead of wait for new ones to arrive. Well, that did not go too well. I ripped off one of the castellated pads, one which is used for hSync. So no video. But I a not defeated so easily, so I lifted the cap off the module, soldered a wire directly to the ESP32 chip and ran it to the HSYNC line. Win! .;) I could start some serious testing
4. The relay circuits don't work. Had to redesign somewhat.
5. I accidentally used the J-leg footprint for the IM41's instead of the Gullwing footprint.
6. Many of the large capacitor's footprints were simply wrong and I had to solder one vertically.
7. I used tantalum caps in the relay circuits, but there's no reason to not use the much cheaper normal electrolytic caps.
8. I think I should add one or more extra holes for screws around the VGA connector because I don't believe for a moment that this is stable. 
</sub>

<sub>After a fixing all of the above, and refactoring a few things on the board, version v0.6 was arrived at. And once again I had boards made by JCPCB. 17 euro for 5 boards. Still cheap, but no discount for me this time. The new boards arrived in record time, and I went to populate one.

Observations:

1. Still have one capacitor's footprint wrong. Although this I was able to fit it horizontally at least.
2. CP2102 solders much easier with the larger sized pads. But it still is fiddly to hand-solder a QFN-28 chip. I wish Silicon Labs would make a QFP-28 version.
3. The board is detected by my Macbook, but can't communicate with it. My Windows machine is luckily able to communicate and flash, but it doesn't seem too happy about it all. What's going on? Turns out that I had chosen ESD diodes for their size, but did not realize that with their size also comes a high capacity of some 100pF. And that's enough to mess up the USB communications. So it's back to the ol' drawing board here. Removing the ESD diodes makes USB work again on all my machines. But i want them, so we'll need a new revision already. 
4. My new relay circuits work! Flawlessly even!
5. Why did I not add an option to keep the amplifier circuit unpopulated and have a line-out instead of a speaker-out? Or why not both options, selectable throguh solder bridges?

Apart from these issues, uTerm2-S v0.5 works!

</sub>

<sub>I just finished the v0.6 design with fixes for all of the above. That would be the final design, with only a slight feature creep (being able to omit/bypass the speaker amplifier). Now we need software...</sub>

This design is available under GPL License V3. This means that I DO NOT release it for commercial use, unless with my explicit written and signed consent. Please contact me if you have commercial questions.

uTerm2-S: https://hackaday.io/project/181583-uterm2-s-a-multi-emulation-color-rs232-terminal  
fabgl: http://www.fabglib.org/, https://github.com/fdivitto/FabGL
