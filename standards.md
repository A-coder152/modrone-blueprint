# Standards
This file is just for standards for different things like mounting, power etc. 
ofc not final because nothing is but this one is like swiss cheese levels of chopped

The goal of this standard is to make the drone easy to:
- design
- assemble
- repair
- expand

This standard is only concerned with things like
- how modules mount
- how modules are oriented
- how low-current power and control signals move
- how the battery connects
- how arms connect mechanically

NOT standardizing everything cause thats too much work for rn

## Design rules

### 1. Standardize module boundaries only
Each block should have a predictable external interface, but does not need to expose every internal subsystem.

Why:
That's the only part where standardizing makes sense lol
learn from lego: standards only matter when its the connections 
(circle things go into circle thing holes is the only rule)

### 2. Separate high-current and low-current
Battery and motor power should not share the same connector as low-voltage stuff.

Why:
To reduce wiring mistakes, electrical noise, and damage risk.
It also makes debugging much easier.

### 3. The center body is.... the center lol
All major modules connect to the center body.
Modules do not daisy-chain through each other (for now).

Why:
A hub layout is easier to understand and keeps the mass and wiring centralized.
note: this can and likely will be changed in the future, if necessary.
But starting off with this design rule is just so I don't get stuck later

### 4. Every module has a defined orientation
Every block has:
- a front direction
- a top side
- a connector side
- a pin 1 marking where applicable

Why:
Ask IKEA
mostly because I am the type of person to flip everything and not notice

## Coordinate/Orientation Standard

All blocks use the same coordinate system:

- +X/-X = forward/backward
- +Y/-Y = right/left
- +Z/-Z = up/down

Each module drawing and CAD file should include:
- a front arrow
- a top indicator
- connector labels
- mounting references

### Why
To make things consistent across
- CAD
- PCB placement
- wiring diagrams
- firmware orientation setup

It's straightforward fs but you can't go wrong documenting everything just in case
also I keep flipping around positive and negative Y which will cause issues if I do it mid design

This next part is inspired by gpt cause i cannot write it well
## Interface Classes

v1 uses four interface classes:

- **M1** = main block mounting interface
- **C1** = low-current control and utility interface
- **P1** = battery power interface
- **A1** = arm mechanical interface

### Why these four
These map to the real system boundaries:

- M1 handles physical modularity
- C1 handles low-power inter-block communication
- P1 handles battery connection safely
- A1 handles repeated structural arm replacement

This is enough structure for a real modular system without overcomplicating the first version.

## M1 Main Block Mounting Interface

### Applies to
- center body top/bottom accessory positions
- payload block
- battery block
- optional sensor or guard blocks

### Standard
**20 mm x 20 mm square mounting pattern wirh4 mounting holes**.

Rules:
- module origin is at the center of the square
- front edge is aligned to +X
- hole pattern is symmetric around module center
- one visual key must indicate orientation even though the hole pattern is symmetric
- hardware size is kept consistent across block classes where practical

### Mechanical Rules
- blocks mount flush to a defined interface plane
- blocks must not require mirrored versions
- blocks should be installable with standard hand tools
- blocks should be removable without disassembling the entire drone

### Why 20 mm x 20 mm
This is small enough for an indoor-oriented compact platform while still being large enough to:
- mount a payload plate
- support a battery carrier
- provide repeatable CAD alignment
- keep the system modular

### Why 4 holes
Four fasteners resist rotation better than two.
That matters for modules carrying:
- batteries
- speakers
- payload boards

### More orientation safety (yippee)
A symmetric hole pattern is convenient for CAD and manufacturing, but dangerous for assembly.
So v1 requires a second orientation cue such as:
- silkscreen arrow
- notch
- asymmetric label placement
- keyed standoff position

^ chatgpt caught that one lowkey

## C1 Low-Current Interface

### Applies to
- fc to payload link
- other low current stuff

### Purpose
C1 is the standard connector for low current transfer and utility power.

C1 is not used for:
- motor current
- raw propulsion wiring
- camera high-speed signaling
- battery charging current

### Connector Class
C1 uses a keyed locking low-current connector family.
Exact part family may be selected later, but it must satisfy:
- keyed insertion
- positive retention
- small size
- hand assembly friendly
- availability as wire-to-board or board-to-board as needed

### C1 Pinout
Pin names are defined from the perspective of the center body / fc hub.

1. `GND`
2. `+5V_AUX`
3. `+3V3_AUX`
4. `FC_UART_TX`
5. `FC_UART_RX`
6. `I2C_SCL`
7. `I2C_SDA`
8. `INT_OR_ID`

### Pin Descriptions

#### Pin 1: GND
Common signal ground for C1.
This pin is required on every C1 connection.

#### Pin 2: +5V_AUX
Low-current regulated 5V supply for payload-side and accessory-side electronics.

The Pi needs 5V so
Providing 5V on C1 is to keep things simple

#### Pin 3: +3V3_AUX
Low-current regulated 3.3V supply.

Many sensors and low-power logic devices operate at 3.3V (including most of the fc).
Exposing 3.3V makes future expansion easier.

#### Pin 4: FC_UART_TX
Fc UART transmit line.

UART is appropriate for the main link between the fc and the pi.

#### Pin 5: FC_UART_RX
Fc UART receive line.

Same thing as above but the oter way

#### Pin 6: I2C_SCL
I2C clock for low-speed peripheral expansion.

I2C is pretty common so including it now is better than needing it later and not having it

#### Pin 7: I2C_SDA
I2C data for low-speed peripheral expansion.

Pairs with SCL to support sensor expansion without redesigning the connector later.

#### Pin 8: INT_OR_ID
Reserved pin for either:
- module interrupt
- module-present detect
- module ID resistor scheme
- future accessory-specific low-speed signal

Basically a bonus pin. you never know when you need it

### C1 Rules
- C1 is low-current only
- C1 must never carry motor or battery propulsion current
- C1 cables must be keyed
- pin 1 must be marked in schematics, PCB silkscreen, and harness documentation
- payload blocks use UART as the primary flight-to-payload link
- I2C is optional and secondary
- camera signaling does not leave the payload block over C1

### Why UART is the main link
cause its simpler to deal with

### Why I2C is included but secondary
I2C is useful for small sensors, but less ideal as the main processor-to-processor communication link.
included for flexibility but not a requirement

### Why camera is not standardized over C1
Camera interfaces are higher-speed and more layout-sensitive.
Standardizing them at the modular boundary would add complexity and reduce reliability.
In v1, the camera stays internal to the payload block.

## P1 Battery Power

### Applies to
- battery block to center body connection

### Purpose
P1 is the dedicated battery interface.

It carries:
- raw battery power
- return path

It does not carry:
- low-speed telemetry
- 3.3V logic
- Pi control signals
- camera/audio signals

### Standard
P1 is a dedicated high-current two-wire locking battery interface.

Signals:
1. `VBAT_RAW`
2. `GND_PWR`

### Rules
- P1 must be physically distinct from C1
- P1 must not be pluggable into C1 by mistake
- battery connection must be removable
- charging is done outside the drone for now
- battery voltage sensing occurs on the main power/flight side, not through a separate battery data connector

### Why P1 is separate from C1
A universal connector for both:
- battery current
- logic rails
- telemetry
would make the system harder to protect and easier to wire incorrectly.

P1 stays dedicated to battery power only.

### Why charging is external for now
Onboard charging adds:
- safety complexity
- thermal considerations
- additional protection circuitry
- connector ambiguity

Right now the cleanest design is a removable pack charged externally.

### Why battery sensing is done centrally
The flight core needs direct awareness of battery state for:
- low-battery telemetry
- future failsafe logic
- power monitoring

That should be near the flight/power system, not inside a detachable battery block.

## A1 — Arm Interface

### Applies to
- each structural arm connection to the center body

### Purpose
A1 standardizes the arm connection so all four arms are mechanically interchangeable.

### Standard
Each arm uses the same root geometry:
- one arm profile
- one root connection shape
- one fastener pattern
- one assembly orientation rule

Rules:
- no front-left / front-right / rear-left / rear-right unique arm parts in v1
- a single arm design is reused four times
- arm replacement should be possible without replacing the center body
- arms should include a defined wire-routing path or wire-protection path

### Why identical arms
Identical arms reduce
- CAD work
- print/manufacturing complexity
- spare part count
- repair difficulty

### Why A1 is defined primarily as mechanical
The propulsion electrical architecture is still more sensitive to final choices such as:
- ESC placement
- motor wire routing
- propulsion current path
- frame dimensions

So v1 standardizes the arm mechanically first.

### Propulsion Wiring Rule
Propulsion wiring is not part of the generic C1 module connector standard.

Instead:
- propulsion wiring is treated as a dedicated subsystem
- high-current or motor-phase wiring stays separate from low-current modular signaling
- the arm design must provide routing features for propulsion wiring

### Why propulsion is excluded from C1
Motor and ESC wiring have very different electrical requirements from telemetry and low-power expansion.
Trying to combine them would weaken the modular standard instead of improving it.

## Payload Block Interface Rules

### Purpose
The payload block hosts:
- Raspberry Pi Zero 2 W
- camera
- microphone
- amplifier (if needed)
- speaker
- future secret stuff

### External Interface
The payload block connects externally by:
- M1 for mounting
- C1 for low-current communication and utility power

### Internal-Only Interfaces
The following stay internal to the payload block:
- camera ribbon / camera-specific wiring
- mic wiring
- speaker wiring
- local Pi power distribution
- local audio circuits

### Why this split
The payload block should present a clean external boundary.

Externally, the rest of the drone only needs to know:
- how the payload mounts
- how it gets low-current power
- how it exchanges commands and telemetry

## Battery Block Interface Rules

### Purpose
The battery block is a removable energy module.

### External Interface
The battery block connects externally by:
- M1 for mounting
- P1 for raw battery connection

### Rules
- battery block should mount close to the center of mass
- battery block should be removable without disturbing payload wiring
- battery block should not require opening the center avionics volume
- battery replacement should not require soldering

### Why this choice
The battery is a wear item and a tuning variable.
It should be easy to:
- swap
- inspect
- change during iteration

A removable battery block supports both experimentation and maintenance.

## Center Body Rules

### Purpose
The center body is the primary integration node.

### It contains or anchors
- flight core PCB
- power distribution
- arm interfaces
- payload interface
- battery interface

### Rules
- all external module connectors stop at the center body
- the center body defines the global orientation reference
- the center body is the only required permanent structural hub in this design

### Why this choice
This keeps:
- mass centralized
- wires short
- interfaces consistent
- module replacement simple

## Standardization Rules

### Rule 1: No mirrored connectors
Every connector must have a defined orientation, no need to guess stuff like that is very dangerous

### Rule 2: One connector family per interface class where practical
C1 connectors should all come from one low-current connector family.
P1 should be a clearly different battery connector class.

### Rule 3: One module, one clear boundary
A block should expose only the interfaces it actually needs. uhhh duh

### Rule 4: High-speed media stays local
Camera and other high-speed media signals remain inside the pi block in v1.

### Rule 5: High current stays isolated
Battery and propulsion current stay off the low-current module connector standard.
In fact that's the wohle point of C1 not having these stuff lol

### Rule 6 All blocks must show orientation in CAD and documentation
Each block must show:
- front
- top
- connector side
- mounting reference

## What is standardized for this design (blueprint)

Standardized:
- block orientation
- center-body hub model
- M1 mounting pattern
- C1 low-current connector pinout
- P1 battery connector role
- A1 arm interchangeability concept
- separation of power classes

## What is not standardized

not yet standardized:
- exact connector manufacturer/part number
- exact battery chemistry/capacity
- exact ESC placement
- exact propulsion harness pinout
- exact camera connector at the drone-wide boundary
- hot-swap behavior
- automatic module identification protocol

### Why these are not standardized yet
cause im lazy.

Real answer is that these depend on later design decisions like
- final size 
- final mass
- propulsion architecture
- PCB layout
- enclosure geometry

note that it should also be flexible enough to handle different sizes and masses if that makes sense.
"final size" just means the basic version, which will have its cad submitted.
But it should not be an issue if you wanted to do a different size as long as the physics is possible

## Rapid fire points

- the center body is the hub
- mounting is repeatable
- low-current communication is standardized
- battery power is isolated
- arms are mechanically interchangeable
- high-speed and high-current subsystems stay out of the generic connector standard

This makes the platform modular without making it fragile.
