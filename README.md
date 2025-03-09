# saab-93NG-IBUS
A collection of known I-BUS messages found in 2002/2003+ second-generation SAAB 9-3's. PR's welcome.
Some frames have not been verified and came form a trionictuning thread, 9-3ss 2006+

Single-Wire GM-LAN Bus, 33.33kbps

Messages are presented byte-wise, where **b0** is the least-significant-byte.
States are represented in binary and hex, and are provided with a human-readable description.

***WIP most values aren't correct***

## BY ID
### 0x60 -- this is wrong
                  | b0 b1 b2
           ID 060 | 00 00 62
    - b1 - key position
        - (0x00) no key
        - (0x20) LOCK position (key inserted)
        - (0x60) OFF position
        - (0xA0) ON position
        - (0xE0) ST position
    - b2 
        - voltage - have not seen this on bus
        -  0x62 * 135 / 1000 = 13,23v?
 
### 0x91 - Cruise Control State
    - b2 - these are wrong?
        - 0000 0000 (0x00) GUESS Cruise control not setup / brake must be pressed
        - 0010 0000 (0x20) Brake released, cruise control enabled?
        - 1010 0000 (0xa0) Brake pressed
    - b1
        - 0000 0000 (0x00) Cruise control off
        - 0010 0000 (0x20) Cruise control on
### 0x108
    - might be missing something, turbo maybe?
    - b1
        - accelerator pedal
          00 - FF | 0% - 100%
    - b2:b3
        - 16 bit integer for RPM
    - b4:b5
        - 16 bit integer for KPH
    ??
             b0 b1 b2 b3 b4 b5 b6 b7
    - ID 108 21 GG 00 WW QQ 00 00 00
      GG    | 00 - FF | accelerator pedal (confirmed pedal position)
      b2 b3 |         | Engine RPM/4 = real RPM
      b4 b3 |         | Speed/128 = KM/H
      
### 0x220
    - b1:b2
        - Steering wheel angle (16 bit special integer)
        - When MSB is 0, decode as regular 16 bit integer (b1 << 8) + b2.
        - When MSB is 1, subtract 65536 from (b1 << 8), before adding with b2.
        - This results in a range of ~-8600 to ~+8600 representing full wheel range (lock to lock).
        - Unsure what the real-world angle-equivalent of this value is (yet).
### 0x249
    - trunk switch?
    - ID 249 length 8 DATA 04 AE 03 80 80 00 00 00 
### 0x24F
     - b3
         - (0x40) right low beam
         - (0x80) left low beam
    - b4
        - (0x04) right fog light
        - (0x08) left fog light
        
                      b0 b1 b2 b3 b4 b5 b6 b7
    - length 8 DATA | 06 AE 02 00 08 00 08 00    left fog
                    | 06 AE 02 00 04 00 04 00    right fog
                    | 06 AE 02 80 00 80 00 00    left low beam
                    | 06 AE 02 40 00 40 00 00    right low beam

### 0x290
    - b0
        - 0000 0001 (0x01) Windshield washer (pull stick fully in)
        - 0000 0100 (0x04) Horn
        - 0001 0000 (0x10) Flash high beams (pull stick halfway)
        - 0001 1000 (0x18) Toggle high beams (pull stick fully in)
    - b1
        - 0000 0001 (0x01) Wiper single
        - 0000 1010 (0x0A) Wiper intermittent
        - 0000 1011 (0x0B) Wiper slow
        - 0000 1101 (0x0D) Wiper fast
    - b3
        - 0000 0000 (0x00) Default
        - 0000 0001 (0x01) Volume UP
        - 0000 0010 (0x02) Volume DOWN
        - 0000 0011 (0x03) SRC button
        - 0000 0100 (0x04) Voice activation button
        - 0000 0101 (0x05) Seek forward
        - 0000 0110 (0x06) Seek backward
        - 0001 0001 (0x11) NXT button
        - 0001 0010 (0x12) Phone button
    - b4
        - 0000 0000 (0x00) Default
        - 0000 0100 (0x40) Indicator left
        - 0000 1000 (0x80) Indicator right
### 0x300
    - b1
        - Light control panel mode
        - 0000 0000 (0x00) OFF
        - 0010 1000 (0x28) Daytime lights
        - 0001 1000 (0x18) Low-beam lights
        - 1000 0000 (+80) Front-fog lights button
        - 0100 0000 (+40) Rear-fog lights button
### 0x310
    - b1
        - SID-C ESP button
        - 0000 0000 (0x00) OFF
        - 0000 0001 (0x01) Pressed
    - b5
        - SID-C Spare button
        - 0000 0000 (0x00) OFF
        - 0010 0000 (0x20) ON (Toggle)
### 0x320
    - b0
        - Locking status / controls 
        - 0001 0000 (0x10) Driver unlocked
        - 0001 0001 (0x11) Driver locked
        - 0001 0100 (0x14) Unlock Button
        - 0001 1001 (0x19) Lock Button
    - b1
        - Mirror adjustment, triggered by d-pad
        - 0000 0000 (0x00) Default
        - 1000 0000 (0x80) Adjustment in progress
    - b3
        - Mirror adjust DPAD direction (for left mirror only?)
        - 0001 0000 (0x10) LEFT
        - 0010 0000 (0x20) RIGHT
        - 0100 0000 (0x40) DOWN
        - 1000 0000 (0x80) UP
    - 
### 0x340
    - b0
        - Child lock active
### 0x350
    - b0
        - Child lock active
### 0x370
    - b0
        - Front fog lights / reversing light
        - 0000 0000 (0x00) OFF
        - 0000 0001 (0x01) REVERSE
        - 0100 0000 (0x40) ON
        - 0100 0001 (0x41) ON and REVERSE
### 0x380
    - b0
        - Brakes pressesd
        - 0000 0000 (0x00) Default
        - 0010 0000 (0x20) Brakes pressed
    - b1
        - Rear fog lights
        - 0000 0000 (0x00) OFF
        - 0010 0000 (0x20) ON
### 0x460
    - b0
        - 0000 0000 (0x00) Night mode off
        - 0100 0000 (0x40) Night mode on
    - b1
        - Instrument lighting brightness levels 
    - b2
        - Night mode brightness
        - Two independant 8 bit integers
    - b3:b4
        - Brightness sensor
        - 16 bit integer
### 0x490 SID Door state
     - sending the open state on bus shows door open icon on SID, does not activate interior lights.
     - key in lock position does make beeping sound
     - Normal state
        - 0x00 0x00 
     - b1 - door open state
        - 1000 0000 (0x80) Front left
        - 0010 0000 (0x20) Front Right
        - 0000 1000 (0x08) Rear left
        - 0000 0010 (0x02) Rear right

        - 0000 1010 (0x0A) both rear doors
        - 0010 0010 (0x22) front right & rear right
        - 0010 1000 (0x28) front right & rear left
        - 0010 1010 (0x2A) front right & rear both
        - 1000 1000 (0x88) front left & rear left
        - 1000 1010 (0x8A) front left & rear both
        - 1010 0000 (0xA0) both front doors
        - 1010 0010 (0xA2) both front doors & rear right
        - 1010 1000 (0xA8) both front doors & rear left
        - 1010 1010 (0xAA) all 4 doors open
        
### 0x520
    - b0
        - Years after 2000
        - 8 bit int
    - b1
        - Month
        - 8 bit int
    - b2
        - Day
        - 8 bit int
    - b3
        - Hour
        - 8 bit int
    - b4
        - Minute
        - 8 bit int
    - b5
        - Second
        - 8 bit int
