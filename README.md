# nTerm2-S
My rendition of the uTerm2-S. It is in SMD, but hopefully easily hand-solderable for everyone (with maybe the exception of the CP2102).

It has a slightly more usable form the uTerm2-S, I think. Smaller and a little better usable when sitting permanently on a desk. And as an extra, it can switch between normal and cross (null-modem) in software so that you wil never have to search for that null-modem cable ever again, and it can switch between RTS/CTS and DTR/DSR.

Supports RxD/TxD with RTS/CTS. Also supports DTR/DSR in a way. It is possible to switch the RTS/CTS lines to the DTR/DSR lines. It will not really be DTR/DSR, but as the ESP32 is a plenty fast enough DTE, and only the DCE might be slow. Slow DCEs can mostly be handled using RTS/CTS as DTR/DSR. However, if push comes to shove, it might be possible to implement 'real' DTR/DSR in software. Subject to experimentation.

It is based on the FABGL library from Fabrizio Di Vittori.

THIS IS A WORK IN PROGESS! I.E. UNLESS STATED AS WORKING, ASSUME THAT IT DOES NOT WORK!

<sub>NOTE: The relay circuit with the bistable relays is directly out of the OMRON databook, and it allows me to switch the relay but use only power when switching (and some losses through the 470 resistor when switched on: 7mA continuous, which is a huge of a lot better than 30mA continuous when using a normal relay). That circuit sources the power from the input, which is connected to a GPIO. ESP32 GPIO pins can source 40mA and sink 28mA. According to the datasheet, the relay needs 100mW of power to switch. At 3V, that is 33mA, but we use 3.3V, so some 30mA. So when switching 'on', the ESP32 will need to source 30mA and settle at some 7mA (all theoretically, be advised). That is quite a lot, but still within spec. The ESP32 does not have to sink much, as the reverse current will go through the diode directly to ground. Lack of a fuse for my Fluke 175 means that I have only verified that it works, but have not been able to verify the real-life power consumption yet. Therefore: do not try this idea just yet. Fuse was ordered from RS, but they came back to me saying the they don't sell to private persons (why does their website allow me to register and order as a **private person** then?). So that delayed me a bit, having to order from somewhere else. Fluke uses weird unique fuses.</sub>

<sub>Progress! I received my fuses and did some real measurements. I used 3.3V, BAT45 (commit says 1N4148, but I was mistaken, they are BAT45's), BC337, 220u and 470 ohm on my bread board, and measured about 14mA max when switching, and 6.8mA continuous. I expected more than 14mA when switching, but maybe this relay is better than the specs? In any case, I decided to experiment with the C and R. I upped the C to 470uF, which seemed to make the switching more reliable. But more importantly, I increased the resistor in steps to see what the result would be. And found that I could go up to at least 3K9 before the switching would become unreliable. 10K was clearly too much, and I didn't try anything in between 3K9 and 10K. With 3K9, 0.88mA is drawn, and I find that good enough. The low current power led uses 3mA, and I keep that on all the time. 1mA is fine.

Created the boards and sent out to JLPCB. Jeez, it's cheap. 11 euro for 5 boards. Still had a bunch of warnings from the pins/vias of the USB-C connector and ESP32 footprints that I just could not resolve. Decided to take my chances, and JLCPCB accepted the gerbers and has actually already finished and posted it. So I expect the boards to be here in a few days. In the mean time, I've been doing some basic work and redesign for a version 2 of the nTerm2-S, with an SD card slot and psram, to make it more versatile (e.g. PC emulation). However, let's get this first version to work first, the objective is to have a terminal with software switchable null-modem.<sub>



* Fuses for my Fluke 175 multimeter arrived. Experimented with the relay driving circuit. When using 3.3V, 1N4148, BC337, 470u and 3K9, the circuit seems to switch quite reliably. Real circuit is meant to use BAT43 and BC817, but is probably similar enough. Can't switch too fast or the circuit will latch up and stop switching, but 4 times per second seems fine. Circuit uses some 10mA max when switching, and about 1mA continuous when the input is 3.3V. 

This design is available under GPL License V3. This means that I DO NOT release it for commercial use, unless with my explicit written and signed consent. Please contact me if you have commercial questions.

uTerm2-S: https://hackaday.io/project/181583-uterm2-s-a-multi-emulation-color-rs232-terminal  
fabgl: http://www.fabglib.org/, https://github.com/fdivitto/FabGL
