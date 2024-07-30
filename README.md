# Node-RED Meshtastic messages node

This node allows sending and receiving text messages and packets to a Meshtastic network thru a device connected via HTTP. It is based on [Meshtastic.js](https://js.meshtastic.org/) library.

This tool was designed to be super simple  to use. The user does not have to worry about protobufs or any sort of enconding. Inputs and outputs are either plain text or readable JSON strings.

## Features
- Send and receive text messages to/from any device in the mesh
- Supervise/monitor device status
- Subscribe and receive all types of events supported by Meshtastic.js (examples: Atak, Position, Range Test, Map Report, Store and Forward, etc...)
- Send packets to any Meshtastic APP (port num)
- Plug and Play: Node-RED is the only requirement. No additional servers, libraries or external dependencies
- Indirect support to MQTT via uplink/downlink channels
- Forget about protobufs

## Limitations
- Connect directly to MQTT server: this is not supported by [Meshtastic.js](https://js.meshtastic.org/)
- Connect to a device via Bluetooth or Serial (not implemented yet)

## Automatic installation (recommended)
- Search for the package `@danpeig/node-red-meshtastic-msg` in the Node-RED community library or NPM.

## Manual installation
1. Place the project files inside a folder called `node-red-meshtastic-msg` inside the Node-RED base directory (where the `settings.js` is located)
2. Run `npm install ./node-red-meshtastic-msg`
3. Edit `meshtastic-msg.js` and change the relative path of the Meshtastic library according to the existing directory structure. Example: `importSync("../node_modules/@meshtastic/js/dist/index.js")`.

To uninstall, run `npm remove @danpeig/node-red-meshtastic-msg` from the same base directory.

## Known issues
Meshtastic.js library has some bugs and can crash the Node-RED server in the following scenarios:
- Connection interrupted during initialization can cause an infinite loop.
- Sending a packet with `wantResponse` flag set to `true`
- Depending on the installation method, you may have to edit the file `meshtastic-msg.js` and change the path of the ImportSync to the location where Meshtastic library was installed. Example: `importSync("../../@meshtastic/js/dist/index.js")`.

## Examples
An example flow with the acceptable input message formats can be found in the `examples` sub-directory.

![Example flow](resources/flow_example.png "Example flow")

## Bonus
The `experiments_meshtastic.js` illustrates how to use [Meshtastic.js](https://js.meshtastic.org/) library from plain Javascript (No TypeScript, no React, no compilation, no nothing).

## License
This node was created by [Daniel BP](http://www.danbp.org) and is available under the MIT license.

## Version history
- **1.2 (29/07/2024)**
    - Improved documentation
    - Meshtastic visual identity
    - Improved NPM descriptions 
- **1.1 (28/07/2024)**
    - Fixed path to the Meshtastic library when installed from NPM
- **1.0 (28/07/2024)**
    - Initial release     

----
# Node reference guide

This information can also be found in the specific node help pages, directly from Node-RED interface.

## Send text node
Send a text message to the mesh connected device

### Input
- msg.payload (string) :  the text message to be sent to the network
- **\[msg.channel\]** (integer):  the Meshtastic channel number. Defaults to *0* (primary)
- **\[msg.destination\]** (integer):  the destination node number, *broadcast* or *self*. Defaults *broadcast*
- **\[msg.wantAck\]** (boolean):  if the receiving devices should acknowldege receipt. Defaults to *true*

**[]** = optional fields

## Receive text node
Receive a text message from the mesh connected device

### Outputs
- **msg** (json) : object with all package properties
- **msg.payload** (string): text content of the message 

## Receive status node
Receive the status code of the Meshtastic device
    
### Outputs
    
- **msg.payload** (integer): status code of the device

## Receive event node
This node will watch for the defined event and output the payload received.
Typically, the output should be a JSON field but there are some events that report numbers or simple strings.
The events list comes from [Mesthastic.js Event System Class](https://js.meshtastic.org/classes/Utils.EventSystem.html)

### Settings
- **event** (string): select the event to monitor/watch

### Outputs
- **msg** (json): data from the event

## Send packet node
Send a packet to the mesh connected device.
Input packet can be either in the string format (*msg.payload*) or Uint8Array (*msg.byteData*) format.

### Input

- **msg.payload** (string): Will be converted to Uint8Array and used if *byteData* field is not set
- **\[msg.byteData\]** (json) : Data properly encoded as Uint8Array. If set, will be used instead of *payload*. Example [72, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100, 33]
- **\[msg.portNum\]** (integer): Application number. Defaults to *1* (text messsage app).
- **\[msg.destination\]** (integer string): Destination node number, *broadcast* or *self*. Detaults to *broadcast*
- **\[msg.channel\]** (integer): Channel number, defaults to *0* (primary channel)
- **\[msg.wantAck\]** (boolean):  Confirmation, defaults to *true*
- **\[msg.wantResponse\]** (boolean): Defaults to *false*
- **\[msg.echoResponse\]** (boolean): Defaults to *false*
- **\[msg.replyId\]** (integer): Defaults to *null*
- **\[msg.emoji\]** (integer): Defaults to *null*

**[]** = optional fields

## Device configuration node
This will setup the connection to the Meshtastic node.
The connection protocol is HTTP. Serial, Bluetooth or MQTT are not supported.

### Options
* **IP or hostname** (string) : IP address or hostname of the Meshtastic device to connect. Examples: *192.168.0.15*, *meshtastic.local*
* **Use TLS** (boolean): If true, the connection will be performed using TLS (encrypted). Default is *false* as most of the devices are not configured for it.
* **Fetch interval** (integer): Interval between polling data from the device. Default is *5000ms*.
