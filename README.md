
# raw-socket-sniffer - Packet capture on Windows without drivers

This repository contains the source for a program which demonstrates how to
capture IP packets on Windows using just raw sockets.

It requires no additional software, such as WinPCAP or npcap, and will simply
use existing operating system functionality.

The program saves captured packets to a file in PCAP format so that it can be
opened on a separate host with a tool which can read that file format
installed.

An implementation in PowerShell and the C programming language is given.  The
C program is considerably faster than the PowerShell program, and it also has
the advantage that Windows Firewall can grant permissions to a specific program
instead of all `PowerShell.exe`

# Ethernet Headers

The programs in this repository use raw sockets to capture IP packets.  A side
effect of this is that layer 2 headers, i.e. the ethernet header, is not
included in the data received.

If it is required to capture ethernet header data then another tool should be
used as valid ethernet headers are not captured by this tool.

Since these tools produce PCAP files, and PCAP files include ethernet, a fake
ethernet header is synthesized for each packet using the all zeros source and
destination ethernet addresses.

This does not affect the other protocol layers, and, for example, TCP streams
can still be reassembled, and IP layer source and destination addresses are
all still valid.

# Implementation in PowerShell

Transfer the `raw-socket-sniffer.ps1` program to the host on which packet
capture should be performed.  Then run the following command to capture packets:

    PowerShell.exe -ExecutionPolicy bypass raw-socket-sniffer.ps1 `
            -InterfaceIp "127.0.0.1" `
            -CaptureFile "capture.cap"

Replace `127.0.0.1` with an IP address from the network interface for which
packets should be captured, and the file `capture.cap` with the name of the
file to which to write packets.

If the Windows Firewall is enabled it will likely require an update to allow
both inbound and outbound packets to be captured.  For the PowerShell script
the `PowerShell.exe` program must be permitted similar to the following:

    netsh advfirewall firewall add rule name="Windows PowerShell" dir="in" action="allow" program="%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe"

Once finished simply press `CTRL+C` to stop the program.

Transfer the `capture.cap` to a host with Wireshark installed (or another
similar program), and then open the `capture.cap` file.

# Implementation in C

Once the project has been checked out simply run the following command to
compile the C program:

    cd <repository>
    nmake

The file `raw-socket-sniffer.exe` will be placed in to the root of the
repository.

Transfer the `raw-socket-sniffer.exe` program to the host on which packet
capture should be performed.  Then run the following command to capture packets:

    raw-socket-sniffer.exe 127.0.0.1 capture.cap

Replace `127.0.0.1` with an IP address from the network interface for which
packets should be captured, and the file `capture.cap` with the name of the
file to which to write packets.

If the Windows Firewall is enabled it will likely require an update to allow
both inbound and outbound packets to be captured.  For the C program it must be
permitted similar to the following (here the `raw-socket-sniffer.exe` program
should be specified as its full path):

    netsh advfirewall firewall add rule name="Windows PowerShell" dir="in" action="allow" program="<path-to>\raw-socket-sniffer.exe"

Once finished simply press `CTRL+C` to stop the program.

Transfer the `capture.cap` to a host with Wireshark installed (or another
similar program), and then open the `capture.cap` file.

# Changes

## Version 1.0.0 - 12/09/2018

 * Initial version

## Version 2.0.0 - 20/09/2018

 * Document how ethernet headers are synthesized and why
 * Review standard compile options
 * Document firewall requirement
 * Implement a PowerShell raw socket sniffer

# License

Copyright 2018 NoSpaceships Ltd

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
