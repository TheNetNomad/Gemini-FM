# Gemini FM
Gemini FM is a music replayer for the MSX series of computers supporting the same song format for devices based on the Yamaha OPLL and Yamaha OPM sound chips, such as the Panasonic FM-PAC (OPLL) and Yamaha SFG-01 (OPM). Gemini FM imposes some minor limitations on OPLL usage and major limitations on OPM usage in order to create a sound module format that works for both chips. The hope is that developers and composers looking to add OPLL music to their MSX games will accept these limitations to instantly also support the much less commonly supported OPM as well. The MSX has many many sound add-ons, but most are not widely supported because they are not widely owned, and not widely owned because they are not widely supported. Gemini FM hopes to solve this chicken-and-egg problem for one of the MSX's oldest and coolest sound modules.

# Developement Status
Gemini FM is very early in developement. All OPLL hardware functions have been implemented and tested, and the bytecode interpreter has been implemented but remains largely untested. No OPM functions have been implemented yet. Once both chips and the intereter are operational, the actual sound driver code will be removed from the MSX-DOS executable we are currently creating to allow it to be used in BASIC/ASM/C/et cetera programs. Software to create music in the Gemini FM format will also be required, likely a MIDI converter and/or an MML compiler, as well as a command-line music player. 

# Compiling
Gemini FM is written in a programming langauge called PARASOL. PARASOL is available [here](http://www.cpm.z80.de/develop.htm). The PARASOL compiler is a CP/M-80 program, so it must be used on a CP/M-80 device or in an emulator such as [iz-cpm](https://github.com/ivanizag/iz-cpm). While CP/M programs are largely compatble with MSX-DOS, the PARASOL compiler has a number of issues with it so compiling on MSX may not work correctly. To compile in CP/M, enter the directory containing the SRC file and the compiler and type:

`PARASOL GEMINIFM.COM`

Or to compile using iz-cpm without actually entering the CP/M command line on your hip and fancy modern machine, enter the directory containing the SRC file and the compiler and type:

`IZ-CPM PARASOL.COM GEMINIFM.COM`

Either of these will create the GEMINIFM.COM executable for use in MSX-DOS. Please note that a variable or variables containing song data must currently be in TEST1.CPY in order for this to compile and play correctly. This will not be required in later builds.

# Music Format
Music data for Gemini FM is stored in pairs of bytes, the first of which containing a bytecode and the second of which containing data. The list of bytecodes and their expected data is as follows:

## Bytecode Commands
| Bytecode | Command | Data | 
| --- | --- | --- |
0x01 | Set Tempo | Length in clock cycles of one wait  | 
0x02 | Key Off  | Channel (0-6)| 
0x03 | Wait | Number of waits | 
0x04 | Loop | Loop value* | 
0x0F | End | Ignored | 
0x10 | Channel 0 Key On | Note (40 = C4) | 
0x11 | Channel 1 Key On | Note | 
0x12 | Channel 2 Key On | Note |
0x13 | Channel 3 Key On | Note | 
0x14 | Channel 4 Key On | Note | 
0x15 | Channel 5 Key On | Note | 
0x16 | Channel 6 Key On | Drum Value** |
0x20 | Channel 0 Set Instrument | Instrument corresponding to OPLL ROM presets (0-15, user voice hardcoded as bell) |
0x21 | Channel 1 Set Instrument | Instrument |
0x22 | Channel 2 Set Instrument | Instrument | 
0x23 | Channel 3 Set Instrument | Instrument | 
0x24 | Channel 4 Set Instrument | Instrument | 
0x25 | Channel 5 Set Instrument | Instrument | 
0x30 | Channel 0 Set Volume | Volume (0-15) | 
0x31 | Channel 1 Set Volume | Volume | 
0x32 | Channel 2 Set Volume | Volume |
0x33 | Channel 3 Set Volume | Volume | 
0x34 | Channel 4 Set Volume | Volume | 
0x35 | Channel 5 Set Volume | Volume | 
0x36 | Bass Drum Set Volume | Volume | 
0x37 | High Hat Set Volume | Volume | 
0x38 | Snare Drum Set Volume | Volume | 
0x39 | Tom Set Volume | Volume | 
0x3A | Cymbal Set Volume | Volume |  
0x40 | Channel 0 Set Detune | Detune value (added to frequency of note) | 
0x41 | Channel 1 Set Detune | Detune value | 
0x42 | Channel 2 Set Detune | Detune value |
0x43 | Channel 3 Set Detune | Detune value |
0x44 | Channel 4 Set Detune | Detune value |
0x45 | Channel 5 Set Detune | Detune value |

All values 0-255 unless otherwise stated.

## Values for Loop Command
| Loop Value | Description | 
| --- | --- |
| 0 | Set infinite loop point |
| 1 | Return to infinite loop point |
| 2 | Set finite loop point |
| 3 | Return to finite loop point on first pass, ignore on second |

Simply put, finite loops repeat once before continuing and infinite loop repeat endlessly. A finite loop nested in an infinite loop will repeat one (or rather play twice) once per infinte loop.

## Values for Channel 6 Key On Command
| Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Must be 1 | Ignored | Ignored | Bass Drum | Snare Drum | Tom | Cymbal | Hi-hat | 

This convienently corresponds to the data format that is written directly to the OPLL's rhythm register. The OPM can only play two drums at once in this configuration, but the sound driver will prioritize your five logical drums into the two drums that sound. OPM channel 6 will play toms and bass drum, giving bass drum priority. OPM channel 7 will play cymbals, snare, and hi-hat, giving priority in that order.
