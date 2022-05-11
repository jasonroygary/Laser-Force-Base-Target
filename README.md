# Laser-Force-Base-Target
Source code for creating your own Laser Force Laser Tag Base or Target (https://laserforcetag.com/)

This is code I wrote in 2008-9 for imitating the Laser Force IR protocol on Generation 5 (current gen is 8). The bases were implemented a upside down frisbies using drilled in RGB LEDs and IR LEDs. The target platform for this code is Arduino 8MHz devices at 3.3V. Specifically these bases I created used Arduino FIOs (https://www.arduino.cc/en/pmwiki.php?n=Main/ArduinoBoardFio) with XBee Generation 1 radios.

These handmade bases were made for a cost of around $30 (Laser Force charged hundreds of dollars per base). The player just needs to shoot at the base or target to gain the points.

The code should be helpful for understanding the IR protocol used in the Laser Force bases and emulating it. I have no idea if Laser Force in Gen 6 or 7 has replaced the IR protocol as I sold my laser tag businesses. But if they have not this code should still work fine for adding additional targets.
