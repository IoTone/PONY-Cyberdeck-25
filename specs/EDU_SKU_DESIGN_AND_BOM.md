COMPUTE
- Cortadodeck 720 (Genio 720)

DISPLAY
- 5" MIPI-DSI panel
- display FPC (pitch/pins per panel)
- clear lens, acrylic or glass
- OCA bonding sheet
- perimeter foam/VHB gasket + primer
- screen bracket / pressure plate (printed)

POWER
- 1S LiPo pouch, ~5000 mAh, integrated PCM + NTC (e.g. LP104770)
- JST-PH battery lead
- USB-C charge jack (5V/BC1.2)
- power button, momentary tactile to PMIC PWRKEY

THERMAL (passive) - I dont know how much of this is needed.
- PTM7950 phase-change pad
- graphite film, PSA-backed on aluminum-foil laminate
- aluminum spreader shim
- Kapton dielectric layer
- thermal gap pad, spreader to shell
- standoff-limited foam-backed bracket
- two side vent sets, flanking screen area (enclosure feature)

INPUT
- 2x ams AS5013 Hall nub IC — [info](https://www.arrow.com/en/products/as5013-iqft/ams-osram.html)
- 2x diametric NdFeB magnet specifically ams MA2H-1 [info](https://www.bomatec.com/en/products/magnets/sensormagnets#specifications
)- Cortadodeck 720 (Genio 720)

DISPLAY
- 5" MIPI-DSI panel
- display FPC (pitch/pins per panel)
- clear lens, acrylic or glass
- OCA bonding sheet
- perimeter foam/VHB gasket + primer
- screen bracket / pressure plate (printed)

POWER
- 1S LiPo pouch, ~5000 mAh, integrated PCM + NTC (e.g. LP104770)
- JST-PH battery lead
- USB-C charge jack (5V/BC1.2)
- power button, momentary tactile to PMIC PWRKEY

THERMAL (passive) - I dgnet (size per nub design)
- 2x silicone centering dome
- 2x printed nub cap
- RP2040 board + external SPI flash
- internal USB lead, RP2040 to board host port
- Rii 518BT keyboard

AUDIO
- micro speaker, 8 ohm (e.g. Same Sky CVS-1508)
- speaker grille (printed)
- mic pinhole + acoustic mesh + silicone boot (onboard AIN1 mic)
- 3.5mm TRRS jack (e.g. Same Sky SJ-435107RS)

ENCLOSURE
- MJF PA12 shells, front + rear
- heat-set brass inserts, M2/M2.5
- M2/M2.5 machine screws + standoffs
- Back panel which (maybe optionally, maybe always) comes manufactured with LEGO studs, with customizable smooth-panel LEGO art designs on the back. Comes with brick separator and customized set of smooth panel LEGOs per the user's selected and or configured model. 

INTERCONNECT
- JST-PH 2.0 (battery)
- JST-SH 1.0 (signal)
- 26-30 AWG silicone wire

One incorrect thing about this render is that the plastic chassis is not properly coming over what should be the screen bezel. In current design, screen bezel is required to be fitted underneath chassis.

 Also the surface finish / material are up for discussion. (Glossy to match the LEGOs on the back? Or matte like the render, or in between). 

In addition I thought it made sense to have 2 not one directional stick pad, for ambidextrous use, visual appeal, and redundancy for UX continuity if one eventually gives out/dies. 

Visual note: surface decals like the speaker holes and USBC port were added by Gemini for visual fidelity. Surface details like the USBC port and the Power Button are scaled slightly too large Gemini's render, making the device appear (slightly) smaller than it's true dimensions. 

<img width="1536" height="2752" alt="Image" src="https://github.com/user-attachments/assets/50b34dca-0c3d-4514-978d-34fbc1a22f93" />


Here is information about what a potential swap of joysticks for touchscreen would involve, and some UX concept for the two. 

<img width="1024" height="572" alt="Image" src="https://github.com/user-attachments/assets/41c17350-02fb-4097-b00f-5c0c0424d363" />

Thumb stick design

Pros
- 14-15 dollars about 11 dollars cheaper than touchscreen
- Thumbs stay on the keyboard, no reaching up to the screen
- Precise cursor control, good for small on screen buttons and window edges
- Hall effect sensor, no drift, durable
- Screen never gets touched, so no fingerprints or smudges

Cons
- Slight learning: right pad moves the cursor, left pad is a separate direction pad for switching workspaces, opening launcher, closing windows
- Not pick up and go, takes a moment to learn (can be mitigated by voice-first / hands free interaction mode)
- More parts to assemble
- Cursor movement isn't as easy touching the screen (can be mitigated by directional moving field selection optionally instead of pointer) 

Touchscreen design

Pros
- Tap or drag where you want, no cursor to move
- Familiar right away, works like phone/tablet
- Fewer parts to assemble
- One surface does both pointing and gestures

Cons
- ~ 25 dollars for the touch panel, about 14 dollars saved by removing the nub parts, so 11 dollars more costly design overall
- Have to lift a hand off the keyboard to touch the screen every time
- A finger is bigger than most small buttons or window edges on this kind of interface, so miss-taps are likely unless the on screen elements are larger
- Screen gets smudged
- Still a little UX learning, ie how to swipe for launcher and work spaces

