# ShowNET - User Documentation

ShowNET is a series of flows and nodes within Node-RED that extend the functionality of Show Technologies devices. Features include:

### C44 Control
    * ✅ Send and receive control messages to C44s over Ethernet
    * 🔜 Configure and control C44s over Serial USB

### Simplified OSC Routing
    * ✅ Send and receive OSC messages to most show control software and some non-traditional entertainment software
    * ✅ Switch, route, and change OSC messages in software
    * 🔜 Easy to program logic for C44 control - if/then statements, loops, etc.

# Getting Started
    * First, determine what computer you want to use to route OSC messages for your show control system. This can be a dedicated machine, a machine running another entertainment control software, or a small server host machine like a raspberry pi. When selecting a computer, consider that ShowNET's graphical user interface can be accessed remotely over Ethernet--you don't need a monitor for your ShowNET machine.
    * You will need to install Node-RED and node.js on the computer. Installation instructions can be found here:  https://nodered.org/docs/getting-started/
    * The Node-RED project hosted here includes all the additional nodes you will need. Those nodes are:
      * OSC: https://flows.nodered.org/node/node-red-contrib-osc
      * SLIP Decoding and Encoding: https://flows.nodered.org/node/node-red-contrib-slip
      * Serialport (Included with more recent installations of Node-RED): https://flows.nodered.org/node/node-red-node-serialport 
      * 
    
