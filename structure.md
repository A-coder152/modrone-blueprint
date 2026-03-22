# structure

## Design Goal
look at scope.md
basically there's going to be additional compute planned, wouldn't want everything on the fc
therefore there is another block

## Flight Core

### Purpose
handling flight functions like:
- reading position and altitude sensors
- estimating orientation
- receiving control inputs
- generating motor commands
- reporting telemetry to the payload block

### inputs
- IMU data
- barometer data
- battery voltage sense
- user control / receiver input
- commands from payload core (for the future)

### Outputs
- 4 motor control signals
- telemetry link to payload core
- status LED / debug output

### voltage
- 3.3V logic rail for MCU and sensors
- battery voltage sense input for monitoring pack level
- **motor power does not flow through the flight controller directly**

### main Parts
- MCU
- IMU
- barometer
- flash or configuration storage if needed
- voltage sensing circuitry
- programming/debug header
- motor signal connectors
- payload communication connector

### connects To
- Power block
- Payload core
- Motor system
- Control input

### Design Notes
Flight controller thingies are all handled in.. flight controller.. so thing can.. fly..

Motor outputs are just the signal, the power is from the power block.

Sensor processing and control loops stay on the flight controller.


## Payload Core

### Purpose
Hosts the non-flight features:
- camera
- microphone
- speaker
- anything else 

### Inputs
- regulated power from power block
- telemetry from flight core

optional inputs 

- optional camera and microphone data
- optional network or user interaction input

### Outputs (future focused ig)
- commands to flight core
- audio output to speaker
- video stream or onboard perception data
- logs and diagnostics

### Voltage
- **5V rail** for Pi and other things
- idk is that all

### Main Parts
- Raspberry Pi Zero 2 W
- camera module
- microphone
- small amplifier (if needed)
- speaker
- payload communication connector to fc

### Connects To
- Power block
- Fc
- Camera
- Audio thingies

### Design Notes
If the Pi crashes, reboots, or becomes overloaded, the drone should still be flying.

this block is basically where the stupid stuff takes place. 
if its too stupid, i can just leave it off. simple right


## Power Block

### Purpose
ya dont want the motors and the flight controller doing stupid stuff together do ya

### Inputs
- battery pack input

### Outputs
- raw battery power to motors or motor path
- regulated 5V rail for pi
- regulated 3.3V rail for fc logic and sensors
- battery voltage sense feed to fc (if needed)

### Voltage
- Battery input: pack voltage, depends on final battery choice
- 5V regulated rail: pi
- 3.3V regulated rail: MCU and sensors
- raw battery rail: motor path

### Main Parts
- battery connector
- power distribution traces or harness
- 5V regulator
- 3.3V regulator
- filtering capacitors
- protection and measurement circuitry

### Connects To
- Battery
- Flight core
- Payload core
- Motor/ESC system

### Design Notes

when the motors suck, you dont want the flight controlelr to suck too.

## sturcture connectors thingies

### purpose
like the actual lego parts. ezpz structure, take them off put them on i guess

### Blocks
- Center body block
- Arm blocks
- Payload block
- Battery block
- Optional guard/duct block

### Mounting Standard
- one common mounting pattern for all interchangeable blocks
- fixed reference orientation so each block has a defined front/back and top/bottom
- center body acts as the mechanical and electrical hub
- whats the standard? good question! thats a problem for future me

### Connector Standard
- one standardized internal connector family for block connection wiring
- separate lines for:
  - power
  - flight telemetry/control
  - optional payload signals
- keyed orientation to reduce assembly mistakes
- again dont ask what the standard is, you'll find it probably in some other file
- ikea is calling, they want their business model back

### Design Notes
The frame is designed as a system of blocks. why? stop asking so much. i want to okay.

Arms should be replaceable individually so crashes do not require fixing up the whole frame.
3D printing is my pookie bear dont wanna be using it for hours every single oopsie tho.

The payload block (pi) is be isolated so it can easiyl be swapped.

The center body is where the fc goes cause thats how smart people do it (not smart just yet, fake it till you make it)

## System notes
this is mostly just regurgitating things but its still important ig

1. If one thing breaks, everything else should be safe
2. Swapping structure should be relatively easy
3. The design is more indoors focused so that means things like making it compact and quiet are priority over just pure power
4. this is just a v0.00067 and things can and will change
