# nTerm2-S
My rendition of the uTerm2-S. In SMD, but hopefully easily solderable for everyone (maybe not the CP2102, but let's see).\

It has a slightly more usable form the uTerm2-S. As extra, it can switch between normal and cross (null-modem) in software so that you wil never have to search for that null-modem cable ever again.\

Supports RxD/TxD with RTS/CTS. Also supports DTR/DSR in a way. It is possible to switch the RTS/CTS lines to the DTR/DSR lines. It will not really be DTR/DSR, but as the ESP32 is a plenty fast enough DTE, and only the DCE might be slow. Slow DCEs can mostly be handled using RTS/CTS as DTR/DSR. However, if push comes to shove, it might be possible to implement 'real' DTR/DSR in software. Subject to experimentation.\

It is based on the FABGL library from Fabrizio Di Vittori.\

THIS IS A WORK IN PROGESS! I.E. UNLESS STATED AS WORKING, IT DOES NOT WORK!\

<sub>NOTE: The relay circuit with the bistable relays is directly out of the OMRON databook, and it allows me to switch the relay but use only power when switching (and some losses through the 470 resistor when switched on: 7mA continuous, which is a huge of a lot better than 30mA continuous when using a normal relay). That circuit sources the power from the input, which is connected to a GPIO. ESP32 GPIO pins can source 40mA and sink 28mA. According to the datasheet, the relay needs 100mW of power to switch. At 3V, that is 33mA, but we use 3.3V, so some 30mA. So when switching 'on', the ESP32 will need to source 30mA and settle at some 7mA (all theoretically, be advised). That is quite a lot, but still within spec. The ESP32 does not have to sink much, as the reverse current will go through the diode directly to ground. Lack of a fuse for my Fluke 175 means that I have only verified that it works, but have not been able to verify the real-life power consumption yet. Therefore: do not try this idea just yet. Fuse was ordered from RS, but they came back to me saying the they don't sell to private persons (why does their website allow me to register and order as a **private person** then?). So that delayed me a bit, having to order from somewhere else. Fluke uses weird unique fuses.</sub>\

This design is available under GPL License V3. This means that I DO NOT release it for commercial use, unless with my explicit written and signed consent. Please contact me if you have commercial questions.\

uTerm2-S: https://hackaday.io/project/181583-uterm2-s-a-multi-emulation-color-rs232-terminal\
fabgl: http://www.fabglib.org/, https://github.com/fdivitto/FabGL
