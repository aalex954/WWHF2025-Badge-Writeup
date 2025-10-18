# Wild West Hacking Fest 2025 Badge Writeup

Solution to the Badge CTF for WWHF 2025 Deadwood

**Team**: `No_Use_For_A_Name`

- aalex954
- Dutch-Sec
- eao0418
- darth_vad3r

<table>
  <tr>
    <img width="75%" height="20%" alt="image" src="https://github.com/user-attachments/assets/f7b349c0-9ae3-4a5c-a2d3-35727bed45fa" />
    <img width="25%" height="20%" alt="image" src="https://github.com/user-attachments/assets/ddbb0c1b-7b6c-422f-af16-778b846725bb" />
  </tr>
</table>

## Sections

- [Challenges](https://github.com/aalex954/WWHF2025-Badge-Writeup?tab=readme-ov-file#challenges)
  - [BIOS](https://github.com/aalex954/WWHF2025-Badge-Writeup?tab=readme-ov-file#bios-challenges)
  - [Shell Windows](https://github.com/aalex954/WWHF2025-Badge-Writeup?tab=readme-ov-file#bios-challenges)
  - [BLE Communication](https://github.com/aalex954/WWHF2025-Badge-Writeup?tab=readme-ov-file#bonus-challenges---communication)
  - [Decoding](https://github.com/aalex954/WWHF2025-Badge-Writeup/edit/master/README.md#bonus-challenges---decoding)
- [Extra - Firmware](https://github.com/aalex954/WWHF2025-Badge-Writeup?tab=readme-ov-file#firmware-extraction-and-strings)

  
## Challenges



The badge CTF included 23 flags divided into two sets of challenges (11 splash screen (BIOS and Shell) and 12 bonus (decoding and BLE))

### BIOS Challenges

```
BHISBIOS © 2008
Version: 8.415
CPU: ESP32-S3
Speed: 240Mhz

150KB SRAM OK
8MB Internal Flash
nrf bluetooth initialized
```

#### Badge CTF - BIOS Challenge 1

- Q: What is the version of the BIOS displayed on the badge screen during boot?
  - A: 	`8.415`
    
 ---
 
#### Badge CTF - BIOS Challenge 2

- Q: How many kilobytes of SRAM does the badge microcontroller have?
  - A: 	`512`

---

### Shell Window Challenges

The shell window challenges are presented as a random splash screen displayed when the badge boots. 
The splash screens can be viewed at boot or by dumpging the firmware and observing the embedded strings. 

![BIOS](https://github.com/user-attachments/assets/3db63e78-beb9-4eab-b459-6ba89d3df6c7)

---

#### Badge CTF - Shell Window Challenge 1

```
MSFT(R) Win NT(TM)
(C)1985-96 MSFT Corp
C:\> net user Admin
User name      Admin
Full Name      Admin
Comment     L3tM31n!
User's comment      
```

- Q: What is the password associated with the Admin user (case sensitive)?
  - A: `L3tM31n!`

---

#### Badge CTF - Shell Window Challenge 2

```
Rtr#                 
Rtr# config term     
Rtr(config)# snmp-ser
ver community pub RO 
ver community priv RW
Rtr(config)# exit    
Rtr# copy run sta 
```

- Q: What is the read-write SNMP community string value configured in the Cisco router prompt?
  - A: `priv`
 
---

#### Badge CTF - Shell Window Challenge 3

```
root@kali# msfconsole
msf> use ms08-067
msf> exploit
[*] Meterpreter sessi
on 1 opened
msf> session -i 1
```


- Q: What Metasploit module was used to successfully establish a Meterpreter session?
  - A: `ms08-067`	

---

#### Badge CTF - Shell Window Challenge 4

```
The IBM PC DOS       
Ver 1.10 (C)IBM 1981 
A> dir /w
COMMAND COM SYS   COM
DEBUG   COM COMP  COM
DONKEY  BAS LINK  EXE
COMM    BAS BASIC COM
```

- Q: What version of IBM PC DOS is displayed in the shell window?
  - A: `1.10`

---

#### Badge CTF - Shell Window Challenge 5

```
bash-3.2$ uname -a   
Darwin BHIS-MacBook-P
ro.local 15.4.0 Darwi
n Kernel Version 15.4
.0: Fri Feb 26 22:08:
05 CST 2016; root:xnu
-3248.40.184~3/RELEAS
E_X86_64 x86_64
```

- Q: What MacOS kernel version is displayed in the shell window?
  - A: `15.4.0`

---

#### Badge CTF - Shell Window Challenge 6

```
(gdb) disas main
Dump of assembler cod
e for function main: 
0x1135 +0: push %rbp 
0x1136 +1: mov %rsp,%
0x1139 +4: sub $0x10,
0x113d +8: movl $0x0,
0x1144 +15: jmp 0x115
```

- Q: What is the target address for the jmp operation in the gdb disassembly?
  - A: `0x115`

---

#### Badge CTF - Shell Window Challenge 7

```
Copyright (C) 1985   
Commodore-Amiga, Inc.
All rights reserved. 
Version 27.3         
Use DATE to set date 
Sun 01-Apr-90 03:11  
1> dir
      c (dir)
```

- Q: What verion of Commodore OS is displayed in the Commodore shell window?
  - A: `27.3`

---

#### Badge CTF - Shell Window Challenge 8

```
# sshnuke 10.2.2.2 -r
ootpw="Z10N0101"   
Conn to 10.2.2.2:ssh 
Exploit SSHv1 CRC32  
Reset pw "Z10N0101"
Sys open: Level (9)
```

- Q: What password is used with sshnuke to gain access to the remote host at IP address 10.2.2.2?
  - A: `Z10N0101`

---

#### Badge CTF - Shell Window Challenge 9

```
 TO SCAN FOR CARRIER 
  TONES, PLEASE LIST 
 DESIRED AREA CODES  
    AND PREFIXES     
AREA                 
CODE  PRFX NUMBER    
(311) 399-0001   
```

- Q: What phone number is targeted by the war dialer?
  - A: `(311) 399-0001`

---

### Bonus Challenges - Communication

Hitting the right button repeatedly eventually causes a message to be displayed on the screen that gives a starting point: `NRF CONNECT MOBILE APP`

A quick search reveals a mobile app called: `nRF Connect for Mobile`

Using the mobile app we were able to discover and connect to the badge.

<img width="403" height="688" alt="image" src="https://github.com/user-attachments/assets/325ee9d9-c66a-45dc-b0ed-da0b08e9f5c8" />

Enumerating the advertised service and its characteristics uncovered three interesting values.

The first (hexadecimal byte string) provided instructions for the remaining two. 
- Raw value: (0x) `2D-2D-2D-20-43-72-61-63-6B-20-74-68-65-20-48-61-73-68-20-2D-2D-2D`
  - `--- CRACK THE HASH ---`

---

#### Badge CTF - Communication Challenge 1 - SHA1 (1)

- Raw value: (0x) `66-37-30-66-36-33-64-65-66-32-35-34-33-66-37-37-66-66-32-36-38-35-37-39-64-64-36-65-63-65-31-32-64-30-66-37-66-63-37-38`
    - From Hex: `f70f63def2543f77ff268579dd6ece12d0f7fc78`

Using CrackStation.net to crack the SHA1 hash

`f70f63def2543f77ff268579dd6ece12d0f7fc78`	`sha1`	`accessit`

---

#### Badge CTF - Communication Challenge 2 - SHA1 (2)

- Raw value: (0x)   
  - `63-64-63-35-62-30-30-30-38-36-32-33-32-30-64-61-39-31-65-34-64-36-36-62-63-33-33-37-39-32-61-61-62-63-32-32-34-66-66-33`
    - From Hex: `cdc5b000862320da91e4d66bc33792aabc224ff3`

Using CrackStation.net to crack the SHA1 hash

`cdc5b000862320da91e4d66bc33792aabc224ff3`	`sha1`	`darkimage`

---

### Bonus Challenges - Decoding

#### Badge CTF - Decoding Challenge 1 - Light Max

The sensor on the upper left of the badge detects light.

<img width="386" height="481" alt="image" src="https://github.com/user-attachments/assets/38f50f28-ad58-4bce-b4b4-d9c45b94df6a" />

Connecting to the badge using putty on COM3 reveals the light sensor values logged in real time. 
Shining a flashlight on the sensor spiked the value to max and paused the logging

<img width="776" height="451" alt="image" src="https://github.com/user-attachments/assets/a566974f-1fee-4ff2-a797-4b16ca75ad67" />


The following pattern is displayed on the badge screen

<img width="424" height="345" alt="image" src="https://github.com/user-attachments/assets/f985e4eb-15dc-4795-9dbc-9263f03c043e" />

The displayed symbols indicate a Pig Pen Cipher

<img width="283" height="72" alt="image" src="https://github.com/user-attachments/assets/89f4ecf0-3050-4c0f-8560-5e79584dc3ec" />

Decoding the cipher reveals the flag: `SNORT IS IN MY SECURITY PIGPEN`

---

#### Badge CTF - Decoding Challenge 2 - Light Min

The sensor on the upper left of the badge detects light.

Connecting to the badge using putty on COM3 reveals the light sensor values logged in real time.
Blocking all the light until the sensor returns `0` reveals the next pattern. 
The flags displayed indicate a `flag semaphore` coded message.

<img width="392" height="257" alt="image" src="https://github.com/user-attachments/assets/0acd809e-0f7b-4000-9493-b99e955e4cec" />


Using the [following table](https://en.m.wikipedia.org/wiki/Flag_semaphore) we deciphered the message: `PIRATES OFF THE PORT BOW`

---

#### Badge CTF - Decoding Challenge 3 - Triple B64 ASCII

Connecting to the badge with a serial connection using putty on COM3, then hitting the left button produces the following console message: `VXpJMWRtUXllR3hhUjJSc1NVZHNla2xJUW5aa01sWjVTVkU5UFE9PQ==`

Converting from Base64 3 times produced the flag: `Knowledge is power!`

---

#### Badge CTF - Decoding Challenge 4 - Morse Code

Hitting the left button also triggers a blinking light on the bottom left of the screen on the badge. 
Decoding the flashing lights was challenging but generating a graph of the red light intensity helped visualize the dots, dashes, and spaces. 

<img width="1500" height="600" alt="image" src="https://github.com/user-attachments/assets/4c1de150-d7be-4919-8823-5aa0df2111e3" />


The final conversion produced the flag: `CRACKED`

---

#### Badge CTF - Decoding Challenge 5

Hitting the left button triggers a light pattern on the "keyboard" section of the badge. 

<img width="551" height="270" alt="image" src="https://github.com/user-attachments/assets/e3f77afc-5943-4c55-8fbd-b4e674e303bc" />

We transcribed the binary values displayed and converted them as shown below:

```
10001111 10110111 10010110 10001100 10110111 10010110 10010001 10111000
```

- From_Binary('Space', 8)
  - `10001111 10110111 10010110 10001100 10110111 10010110 10010001 10111000`
    - = `0x8F 0xB7 0x96 0x8C 0xB7 0x96 0x91 0xB8`

- NOT() - Bitwise NOT per byte (XOR with 0xFF)
  -  `~8F=70 ('p'), ~B7=48 ('H'), ~96=69 ('i'), ~8C=73 ('s'), ~B7=48 ('H'), ~96=69 ('i'), ~91=6E ('n'), ~B8=47 ('G')`

- ASCII Flag: 
  -  `pHisHinG`

---

#### Badge CTF - Decoding Challenge 6 - ROT 13

Hitting the right button displays one of 6 messages. 

Message 1: `Ovgr gur ohyyrg`

ROT(13) = `Bite the bullet`

---

#### Badge CTF - Decoding Challenge 7 - Caesar Cipher

Hitting the right button displays one of 6 messages. 

Message 2: `Gwjfp ymj nhj`

- Caesar Cipher (right 5 OR left 21) = `break the ice`

---

#### Badge CTF - Decoding Challenge 8 - B64

Hitting the right button displays one of 6 messages.  

Message 4: `QmlyZCBpbiAgdGhlICBoYW5k`

From Base64 = `Bird in the hand`

---

#### Badge CTF - Decoding Challenge 9 - B32

Hitting the right button displays one of 6 messages. 

Message 5: `INZHSIDPOZSXEIDTOBUWY3DFMQQG22LMNM======`

From Base32 = `Cry over spilled milk`

---

#### Badge CTF - Decoding Challenge 10 - Baconian A/B cipher 

Hitting the right button displays one of 6 messages.  

Message 3: `abaab baaaa babba abbba baaba abbab`

Using the substitution table: 

```
A	AAAAA	B	AAAAB
C	AAABA	D	AAABB
E	AABAA	F	AABAB
G	AABBA	H	AABBB
I=J	ABAAA	K	ABAAB
L	ABABA	M	ABABB
N	ABBAA	O	ABBAB
P	ABBBA	Q	ABBBB
R	BAAAA	S	BAAAB
T	BAABA	U=V	BAABB
W	BABAA	X	BABAB
Y	BABBA	Z	BABBB
```

Flag:

    • abaab → K
    • baaaa → R
    • babba → Y
    • abbba → P
    • baaba → T
    • abbab → O

---

## Firmware Extraction and Strings

`λ python -m esptool -p COM3 -b 460800 read_flash 0x000000 0x800000 full_flash.bin`

`λ python esp32_image_parser.py show_partitions ..\..Downloads\full_flash.bin`

<img width="592" height="912" alt="image" src="https://github.com/user-attachments/assets/c742046d-071f-4a66-8b5b-09417293096b" />

`λ Strings.exe -n 8 .\app.bin`

```
BADGE BLE
5b1efd49-02d1-46b3-9e4d-e7385878a19d
08834ffd-fc4e-4e7c-966b-11e6f6b1406e
cdc5b000862320da91e4d66bc33792aabc224ff3
79ff92fb-2276-4fec-ba79-c9b133e8c00e

--- Crack the Hash ---
7d10c989-0fe0-44bb-a65a-64963a9b8ab4
F70f63def2543f77ff268579dd6ece12d0f7fc78

3 Characteristics defined! Now you can read it in your phone!

Client connected timer started
Client connected for too long
Client disconnected timer reset

Ovgr gur ohyyrg
Gwjfp ymj nhj
abaab baaaa babba abbba baaba abbab

QmlyZCBpbiAgdGhlICBoYW5k
INZHSIDPOZSXEIDTOBUWY3DFMQQG22LMNM======
nRF Connect Mobile App
CIPHER CHALLENGE
```

