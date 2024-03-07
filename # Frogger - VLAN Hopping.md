# Frogger - VLAN Hopping
Simple VLAN enumeration and hopping script.

* Sniffs out CDP packets and extracts (VTP domain name, VLAN management address, Native VLAN ID and IOS version of Cisco devices)
* It will enable a DTP trunk attack automatically
* Sniffs out and extracts all 802.1Q tagged VLAN packets within STP packets and extracts the unique IDs.
* Auto arp-scans the discovered VLAN IDs and auto tags packets and scans each VLAN ID for live devices.
* Auto option to auto create a VLAN interface within the found network to connect to that VLAN.

## How to install
You have to retrieve sources from the Github
'git clone https://github.com/nccgroup/vlan-hopping.git'

## How to use
You simply have to execute the script using shell
'./frogger.sh'
Execute it as root

### Requirements
To correctly install and use this tool you'll need the following modules :

- Arp-Scan 1.8 (in order to support VLAN tags must be V1.8 - Backtrack ships with V1.6 that does not support VLAN tags) http://www.nta-monitor.com/tools-resources/security-tools/arp-scan
- Yersina (built into Backtrack)
- Tshark (buit into Backtrack)
- Screen (built into Backtrack)
- Vconfig (built into Backtrack)