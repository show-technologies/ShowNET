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
### Installation
    * First, determine what computer you want to use to route OSC messages for your show control system. This can be a dedicated machine, a machine running another entertainment control software, or a small server host machine like a raspberry pi. When selecting a computer, consider that ShowNET's graphical user interface can be accessed remotely over Ethernet--you don't need a monitor for your ShowNET machine.
    * You will need to install Node-RED and node.js on the computer. Installation instructions for Linux and Windows can be found here:  https://nodered.org/docs/getting-started/. Detailed instructions for Mac are here: https://www.influxdata.com/blog/guide-installing-node-red/
    * The Node-RED project hosted here includes all the additional nodes you will need. Those nodes are:
      * OSC: https://flows.nodered.org/node/node-red-contrib-osc
      * SLIP Decoding and Encoding: https://flows.nodered.org/node/node-red-contrib-slip
      * Serialport: https://flows.nodered.org/node/node-red-node-serialport 
      
### Running Node-RED
   * Navigate to the command line or terminal:
     * On Mac, find the program "Terminal" in the spotlight search or Applications>Utilities folder.
     * On Windows, enter "Command Prompt" or "cmd" in the Windows search bar and run the program Command Prompt.
     * On linux, find the terminal program or press "Control + Alt + t".
   * Enter "node-red" in the terminal. You should see the terminal start to fill with text like this:
![image](https://github.com/user-attachments/assets/1c0962db-d3db-44a4-8d60-c86382b05348)
   * Open a web browser and navigate to "localhost:1880" to access the flow editing window.
   * Users running Node-RED connected to the internet should read through this article on securing Node-RED: https://nodered.org/docs/user-guide/runtime/securing-node-red
   * Node-RED can be configured remotely. If you are planning to configure Node-RED from a different machine than the one running it, for example, if you are running Node-RED on a dedicated control computer but wish to program it from an audio computer, you can access the Node-RED flow editor from a computer on the same LAN by opening a web browser and entering "http://[Node-RED computer's I.P. address]:1880."
   * You will run Node-RED from the command line every time you want to use it, but once your flows are configured, you do not have to access the flow editing window.
    
# Integrating QLab or Other OSC-Compatible Softwares
### Setting Up A LAN
   * This will be a simplified guide to configuring a LAN to connect a program running an entertainment control software to a Show Technologies device like a C44. It will not cover details such as switch/router selection.
   * The first step to setting up a LAN is to determine a subnet and subnet mask to operate on. This will determine the IPv4 addresses that your show control computers and Show Technologies devices will use. The subnet mask determines how many numbers in the I.P. addresses have to match. The subnet 255.255.0.0 needs the first two numbers in every I.P. address to match. Good practice is to use private addresses like 10.x.x.x or 192.168.x.x.
   * Write out a list of the computers and devices (including Show Technologies devices) on your show control network and give them I.P. addresses, ensuring none match and none are in use on other computers on the network.
   * Each computer will need one or more ports to communicate over. Ports can be any number between 1 and 65335, but some are dedicated use ports--make sure not to use those. Add the ports you select to your list. A spreadsheet makes keeping track of this information easy:
![image](https://github.com/user-attachments/assets/ce88957d-4021-42a3-abaa-965502aefa2e)

   * Choose how you will connect each computer to the network (Show Technologies devices are tested on wired Ethernet networks, so that is what we recommend). Configure each device's network adaptor with a static I.P. address matching the one you've written down.
        * Configuring static I.P. on Mac: https://support.apple.com/en-ie/guide/mac-help/mchlp2718/mac
        * Configuring static I.P. on Windows: https://support.microsoft.com/en-us/windows/change-tcp-ip-settings-bd0a07af-15f5-cd6a-363f-ca2b6f391ace
   * Finally, plug all of your devices into the network.

### Patching QLab
*Note: Patching from other softwares is similar, and well documented for most.*
   * Go to File>Workspace Settings>Network
   * We will make a new network patch for Node-RED. Create a new network patch or modify Patch 1. Give the patch a descriptive name (Like Node-RED or Show Control), set type to "OSC Message," network to UDP, interface to Automatic, and destination to your Node-Red I.P. address and port number (Note: if you are running Node-RED on this computer, instead of an I.P. address you will enter "localhost").

### Configuring Node-RED
   * Run Node-RED and navigate to the flow using the instructions above. If this is the first time you have run it on this computer, the Flows panels will be empty.
   * Drag a "udp in" node into your flow. A red arrow will appear on it, which indicates that it is not configured. Double click into the node and set its port to the port assigned above. Give the node a descriptive name and press done. This node is the input for all messages from QLab on this IP address at this port. It is possible to configure multiple UDP in nodes if you wish to divide up your patch.
     ![image](https://github.com/user-attachments/assets/55ceb786-41ea-4356-9d7e-00e63c6aba8f)
  
### Testing Your Patch
   * At this point it is a good idea to make sure your programs can communicate. Add two more nodes to your Node-RED flow: OSC and debug. Configure your debug node to display "msg.topic." On the right hand side of the Node-RED editor window, navigate to the debug pane (indicated with an insect icon).
   ![image](https://github.com/user-attachments/assets/feb77bc8-9b35-4bad-a045-40af1a5f896d)

   * Create a network cue in QLab with an address like "/test." Assign it to your Node-RED patch, and press GO. You should see "/test" appear in your Node-RED Debug window.

### Routing OSC Messages to Multiple Show Technologies Devices in Node-RED
   * Managing multiple Show Technologies devices is easy in Node-RED. Each device needs a "udp out" node. Set the destination I.P. and port per your I.P. list, and give each a descriptive name. It is simplest to give each node the name that you will use in its OSC address.
![image](https://github.com/user-attachments/assets/71cd8fd5-98a5-4821-934c-6e40a437de46)
   * If you are only planning to use one device, simply wire your new udp out node to the udp in node created above. Now Node-RED will route messages directly from QLab to your device.
<details>
<summary> Do I need an OSC node? </summary>
The OSC node intelligently combines or separates a Node-RED message topic and payload into an OSC message, where the topic is equivalent to the address and the payload is the argument. We want our messages to be packaged as a single buffer. QLab (and other control programs) configures OSC messages as the single buffer, so an OSC node will unpackage it into a topic and payload, which will not work. For this reason, best practice is to either wire the UDP in directly to a UDP out, OR to add two OSC nodes in line, so that the message is unpackaged and repackaged. Indeed, when using some softwares, for example TouchOSC, the double OSC node is preferred.
![image](https://github.com/user-attachments/assets/b8ab9dd9-109c-4277-937b-3790d6a4c0c7)
</details>

   * To route between multiple Show Technologies devices, create a "udp out" node for each, following the instructions above.
   * Create a "switch" node. Give the node a descriptive name like "Device Switcher." Set property to "msg.payload." Add outputs for each device that you are routing to. Add output for each device. Set each output to "contains," "string," and the device name section of the OSC address for each device.
![image](https://github.com/user-attachments/assets/26da1d01-89e9-4c29-859f-56290941b3db)
   *Wire each device's udp out node to the corresponding switch output.
![image](https://github.com/user-attachments/assets/3cacf057-13f0-46b0-8a24-e70c840a0b90)

### Writing Cues for Node-RED Devices
   * In QLab, create network cues following the OSC address scheme detailed in your devices' instructions. The patch for each cue determines the Node-RED instance the cue will go to, the device name part of the address will route the cue to each device in Node-RED using the switch node configured above, and the function and arguments of the OSC addresses control the outputs on the Show Technologies device.

### Receiving Messages from Show Technologies Devices
   * Each Show Technologies device has a configurable remote I.P. and port that it will send messages to. You can see and route these messages in Node-RED by creating a udp in port that listens on the port assigned (by default, 9999). Instructions for configuring this I.P. and port will be with the documentation for your device.

