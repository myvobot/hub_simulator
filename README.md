# Host-based Simulation

## Introduction

This repository provides host-based simulation for SyncDisplay Hub ("Stimulator").

SyncDisplay Hub is a typical embedded system. Programming such a system can be extra challenging compared to writing software for a general-purpose computer.

Since embedded systems are harder to debug, we would like to enabling the App programming by hiding the implementation details of the system framework and hardware drivers. This host-based simulation could be useful, especially when you are focusing on web-based or network related modules.

Although we can run Python code on a host computer (MacOS/Windows/Linux), there is no hardware attached to perform the 802.15.4 access to SyncDisplay, or sensors. Thus, to run a full featured program, we need to setup a LAN based development environment.

```
+---------------+           +-------------------+
| Host Program  | <-------> | Hub as a 'bridge' |
+---------------+           ++--------+-------+-+
                             |        |       |
                             |        |       |
            +-----------+    |  +-----+-----+ |     +-----------+
            | display 1 +<---+  | display 2 | +---->+ display 3 |
            +-----------+       +-----------+       +-----------+
```

- The 'Host Program' runs on MacOS or Linux based computer (Dev-host)
- 'Hub' works as a bridge, which connects to all PAN nodes
- The Dev-host and Hub connects to the same network, either over Ethernet or WLAN.

The Hub obtains an IP address, eg. 192.168.0.80. You should be able to ping this IP on the Dev-host.

The Hub stimulates the hardware inputs/outputs automatically, as they are typically physical/external in nature.

## Get Started

### Prerequisites

Before start, the Hub:

- Must be setup and ready to use
- The develop mode must enabled over web portal of the Hub (especially the "Stub Bridge" option)

For macOS:

```
brew install libffi
```

### Run the Host-based Simulator

Assume the workspace path is "~/workspace/hub"

```
cd ~/workspace/hub
git clone https://github.com/myvobot/hub_ports.git
cd hub_ports/<YOUR_SYSTEM_TYPE>
./mp
```
Replace the "YOUR_SYSTEM_TYPE" with macOS or x86_linux, etc.

### Configurate the Simulator

- Web Server Port: The simulator runs a web server portal, sometimes it may conflict with other existing web service in your host computer. Type in a different one when prompted.

- Hub's IP Address: The simulator must connects to a real Hub over the Ethernet or WLAN. Input the IP address, eg. 192.168.0.80

### Debug Your App

The simulator creates some folders. One of them is "app".

- Simply create a text file named app/__init__.py with the content below:

```
import uasyncio as asyncio

class App:
    def __init__(self, mgr, loop, pan):
        # User's App, print a 'hello world' to console every 10 seconds
        self._loop = loop
        self._loop.create_task(self.myLoop('hello world'))

    async def myLoop(self, s):
        while True:
            print(s)
            await asyncio.sleep(10)
```

- Ctrl+C to end the simulator, and run again
- Observe the output of the console, there should be 'hello world' output every 10 seconds

### Deployment

After your App runs smooth and bug-free, you can upload it to the real Hub and runs on a realtime environment.

Kindly note these difference between a simulator and a real Hub:

- Computing power: the Hub is quite slow compare to any modern x86 computer
- Memory allocation: usually less than 2MB RAM
- Code size: less than 256KB in total for the Python code, configs and resources

### Tips

- Modify "conf/cfg_stub.json" when your Hub's IP address changed
- Modify "conf/configs.py", DEBUG_LEVEL to 10, or 20 for more log outputs

