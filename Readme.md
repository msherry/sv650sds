Overview
========

SDS (Suzuki Diagnostics System) is a KWP-2000 (sometimes known as K-Line or 
ISO-14230) protocol running at 10,400 baud.  While the ISO spec covers the 
physical signaling and general protocol, the actual messages and their meaning 
are left up to the manufacturer to define and impliment.

The result is that different manufacturers end up writing incompatible protocols
and that even for a given manufacturer, the protocol may differ for different 
vehicles.

The result is that while AiM can decode the SDS protocol for the Suzuki GSXR, it
can only do a medicore job of doing so for the 03-05 SV650.  The goal of this 
project is to reverse engineer and document the 03-05 SV650 protocol and develop
some tools to make reverse engineering other vehicles easier.

Tools
=====

I have the following tools available to me:

 - Cheap chinese clone of the "HealTech" Suzuki Diagnostic Tool (SDT)
 - Teensy 2.0 + STL9637D K-Line interface 
 - OpenBench Logic Sniffer + OLS

Logic Sniffer
=============

By using a simple voltage divider using a 33k and 10k resistor, I was able to
drop the 12V signal of the K-Line to something the OpenBench Logic Sniffer was
able to process.

Using 50kHz sampling rate and setting a trigger to look for 0x80, I'm able to
capture the initial 25ms handshake between the SDT and ~12 seconds worth of data.

By using the UART Analyser built in to OLS, I was able to generate a simple CSV
file with all the communications.  Next step is to build a suite of tools to 
clean up the data (OLS puts some binary characters in the CSV) and to do some 
basic decoding of the messages to figure out:

 - Decode header to determine if sender is ECU or SDT 
 - Decode payload bytes
 - Build a lookup table to map payload values to something meaningful
 - Convert payload to useful messages


Teensy + STL9637D
=================

Using a simplified version of the code from here: https://github.com/o5i/Datalogger
I was able to validate that the Teensy/STL9637D can communicate to the bike.
Connecting the two is pretty straight forward (just need +12V, GND and the K-Line 
off the bike), but you need to remember a 500-1k pull up resistor on the K-Line.
