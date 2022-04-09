# Gemini FM
Gemini FM is a music replayer for the MSX series of computers. It aims to support one song format for two sound chips: the Yamaha OPLL (Panasonic FM-PAC and derivatives) and Yamaha OPM (Yamaha SFG-01 and derivatives). Gemini FM imposes some minor limitations on OPLL usage and major limitations on OPM usage in order to create a sound module format that works for both chips. The hope is that developers and composers looking to add OPLL music to their MSX games will accept these limitations to instantly also support the much less commonly supported OPM as well. Composers may then take it a step further and replace some OPM voices with secondary voices that sound more less like their OPLL counterparts. This will allow composers to use the additional features of the OPM still without needing to do a second arrangement or include two sets of music data per song. 

The MSX has many many sound add-ons, but most are not widely supported perhaps because they are not widely owned, and perhaps still not widely owned because they are not widely supported. Gemini FM hopes to solve this chicken-and-egg problem for one of the MSX's earliest sound modules.

# Usage

Currently Gemini FM is only available as an MSX-DOS2 program. The ultimate goal is a standalone version will be available for use in other projects. To use as is for now, simply navigate to the directory that contains GEMINIFM.COM and type:

`GEMINIFM.COM [SONGFILE.EXT]`

This will load the program and play the specified song file, or SONG.BIN if not. A disk error will occur if no argument is supplied and no file named SONG.BIN is present.

# Developement Status
Gemini FM is still in developement. Most OPLL and OPM functions have been implemented, but everything needs testing and fine tuning. There may be timing-related bugs in the player still. 

# Compiling
Gemini FM is written in a programming langauge called PARASOL. PARASOL is available [here](http://www.cpm.z80.de/develop.htm). The PARASOL compiler is a CP/M-80 program, so it must be used on a CP/M-80 device or in an emulator such as [iz-cpm](https://github.com/ivanizag/iz-cpm). While CP/M programs are largely compatble with MSX-DOS, the PARASOL compiler has a number of issues with it so compiling on MSX may not work correctly. To compile in CP/M, enter the directory containing the SRC file and the compiler and type:

`PARASOL GEMINIFM.COM`

Or to compile using iz-cpm without actually entering the CP/M command line on your modern machine, enter the directory containing the SRC file and the compiler and type:

`IZ-CPM PARASOL.COM GEMINIFM.COM`

Either of these will create the GEMINIFM.COM executable for use in MSX-DOS. 

# GFMASM
That is GFMASM is to an MML compiler what an assembler is to a high level langauge compiler. This is an intermediary format between the output of a full music composition tool and Gemini's binary music format. This can be written by hand or created with signifigantly more ease with tools like the [Gemini FM MIDI converter](https://github.com/TheNetNomad/GFM-MIDI-Converter). GFMASM is turned from a standard ASCII text file into a Gemini FM binary file by GFMASM.COM, which comes packaged with GEMINIFM.COM

## Syntax
Below is the syntax for writing music in GFMASM. Bracketted letters are the only letters required, so you can write a set instrument command as INST 0 1, or I 0 1, or ILLINOIS 0 1 if you so chose. Please only user upper case letters and place only one command per line.

| Command | Arguments | Argument Format | 
| --- | --- | --- |
[D]ETUNE | X YY | X = channel (0-5), Y = fine tune above base note 00-FF
[I]NST | X Y | X = channel (0-5), instrument (0-1F) 
[K]EY	| ON or OF | This is an optional parameter before key on and off commands for readability 							
[L]OOP [E]ND | n/a | n/a						
[L]OOP [S]TART | n/a | n/a		 			
[OF]F | X | X = channel (0-6) 								
[ON] | X YY | X = channel (0-6), YY = note and octave (E4, D2, A-6, B+4) for channels 0-5 or B, S, M C, and/or H for channel 6
[R]EPEAT E[N]D | n/a | n/a				
[R]EPEAT [S]TART | n/a | n/a					
[T]EMPO | XX | XX = byte 00-FF, length in vblanks (50ths or 60ths of a second) of one wait |  						
[V]OL | X Y | X = channel (0-5) or one drum value (B, S, M, C, OR H), Y = volume 0-F
[W]AIT | XX | XX = 0-FF, how long to wait					
| * | Comment | Comment (ignored by GFMASM)

See EXAMPLE.TXT in latest release for an example of GFMASM input.

# Performance Data Format
Music data for Gemini FM is stored in pairs of bytes, the first of which containing a bytecode and the second of which containing data. This is subject to change as the playroutine evolves. The list of bytecodes and their expected data is as follows:

## Bytecode Commands
| Bytecode | Command | Data | 
| --- | --- | --- |
0x01 | Set Tempo | Length in vblanks of one wait  | 
0x02 | Key Off  | Channel (0-6)| 
0x03 | Wait | Number of waits | 
0x04 | Loop | Loop value (See below table) | 
0x0F | End | Ignored | 
0x10 | Channel 0 Key On | Note (Semitones above C1) | 
0x11 | Channel 1 Key On | Note | 
0x12 | Channel 2 Key On | Note |
0x13 | Channel 3 Key On | Note | 
0x14 | Channel 4 Key On | Note | 
0x15 | Channel 5 Key On | Note | 
0x16 | Channel 6 Key On | Drum Value (See below table) |
0x20 | Channel 0 Set Instrument | Instrument (See below table) |
0x21 | Channel 1 Set Instrument | Instrument |
0x22 | Channel 2 Set Instrument | Instrument | 
0x23 | Channel 3 Set Instrument | Instrument | 
0x24 | Channel 4 Set Instrument | Instrument | 
0x25 | Channel 5 Set Instrument | Instrument | 
0x30 | Channel 0 Set Volume | Volume (0-F) | 
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
| 1 | Return to infinite loop point (always) |
| 2 | Set repeat point |
| 3 | Return to repeat point (once) |

Infinte loops are used to loop a song infinitely, as the name suggests. Repeats will only repeat a section once, but repeats can be nested within themselves and within an infinite loop.

## Values for Channel 6 Key On Command
| Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Ignored | Ignored | Must be 1 | Bass Drum | Snare Drum | Tom | Cymbal | Hi-hat | 

This convienently corresponds to the data format that is written directly to the OPLL's rhythm register. The OPM can only play two drums at once in this configuration, but the sound driver will prioritize your five logical drums into the two drums that sound. OPM channel 6 will play toms and bass drum, giving bass drum priority. OPM channel 7 will play cymbals, snare, and hi-hat, giving priority in that order.

## Instrument Table
| Instrument Value | OPLL Instrument | OPM Instrument | MIDI Program Number 
| --- | --- | --- | --- |
0x00 | Bell | Bell | 32
0x01 | String | String | 1
0x02 | Guitar | Guitar | 2
0x03 | Piano | Piano | 3
0x04 | Flute | Flute | 4
0x05 | Clarinet | Clarinet | 5
0x06 | Oboe | Oboe | 6
0x07 | Trumpet | Trumpet | 7
0x08 | Organ | Organ | 8
0x09 | Horn | Horn | 9
0x0A | Synthesizer | Synthesizer | 10
0x0B | Harpsichord | Harpsichord | 11
0x0C | Vibraphone | Vibraphone | 12
0x0D | Synthesizer Bass | Synthesizer Bass | 13
0x0E | Acoustic Bass | Acoustic Bass | 14
0x0F | Electric Guitar | Electric Guitar | 15
0x10 | Bell | Bell (Alternate) | 16
0x11 | String | String (Alternate) | 17
0x12 | Guitar | Guitar (Alternate) | 18
0x13 | Piano | Piano (Alternate) | 19
0x14 | Flute | Pan Flute | 20
0x15 | Clarinet | Synthesizer 2 | 21
0x16 | Oboe | Saxophone | 22
0x17 | Trumpet | Trumpet (Alternate) | 23
0x18 | Organ | Accordion | 24
0x19 | Horn | Horn (Alternate) | 25
0x1A | Synthesizer 1 | Synthesizer 3 | 26 
0x1B | Harpsichord | Harpsichord (Alternate) | 27
0x1C | Vibraphone | DX Electric Piano | 28
0x1D | Synthesizer Bass | Synthesizer Bass (Alternate) | 29
0x1E | Acoustic Bass | DX Bass | 30
0x1F | Electric Guitar | Synthesizer 4 | 31  

The OPLL has fifteen preset voices and one user voice, which in this engine is hardcoded as a bell. The first half of the OPM sound bank is approximations of these OPLL voices, while the second half are either alternative versions that take advantage of the OPM's enhanced FM synthesis capabilities or alternate instruments altogether. By using only the insturments up to 0x10, you will have a song that sounds *roughly* the same on both chips, while using the second set of voices will allow you to better utilize the OPM without breaking compatability with the OPLL.

The MIDI program column does *not* correspond to the General MIDI instrument set. These program numbers are for the [Gemini FM MIDI Converter](https://github.com/TheNetNomad/GFM-MIDI-Converter).

# Acknowledgements

All new code written for Gemini FM is in the public domain. You may use or modify this software however you see fit. I ask but can't stipulate attribution. This software uses the GETSLT routine provided by the MSX Assembly Page which is *not* public domain and *does* [require attribution](http://map.grauw.nl/disclaimer.php).

This software would not be even remotely possible without the following resouces:
- [MSX.org Forum and Wiki](https://www.msx.org/)
- [MSX Assembly Page](http://map.grauw.nl/)
- [Maxim's Corrected/Annotated OPLL Datasheet](https://www.smspower.org/maxim/Documents/YM2413ApplicationManual?sid=28c370e1fcac51d5774319979bf96f4c)
- [CX5M.net](https://cx5m.file-hunter.com/)
- [The Unofficial CP/M Website](http://www.cpm.z80.de/)
