# nTerm2-S
My rendition of the uTerm2-S. In SMD, but hopefully easily solderable for everyone (maybe not the CP2102, but let's see).

It has a slightly more usable form the uTerm2-S. As extra, it can switch between normal and cross (null-modem) in software so that you wil never have to search for that null-modem cable ever again.

Supports RxD/TxD with RTS/CTS. Also supports DTR/DSR in a way. It is possible to switch the RTS/CTS lines to the DTR/DSR lines. It will not really be DTR/DSR, but as the ESP32 is a plenty fast enough DTE, and only the DCE might be slow. Slow DCEs can mostly be handled using RTS/CTS as DTR/DSR. However, if push comes to shove, it might be possible to implement 'real' DTR/DSR in software. Subject to experimentation.

It is based on the FABGL library from Fabrizio Di Vittori.

THIS IS A WORK IN PROGESS! I.E. UNLESS STATED AS WORKING, IT DOES NOT WORK!

This design is available under GPL License V3. This means that I DO NOT release it for commercial use, unless with my explicit written and signed consent. Please contact me if you have commercial questions.

uTerm2-S: https://hackaday.io/project/181583-uterm2-s-a-multi-emulation-color-rs232-terminal
fabgl: http://www.fabglib.org/, https://github.com/fdivitto/FabGL
