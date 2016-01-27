#Scatterbrain Protocol:  2.01
High bandwidth internet-less 'lazy mesh' with filesharing, service discovery, and no constant connections needed. 


##Overall Goals:
* Adaptable network as a base for many third party protocols
* Low bandwith, low power bluetooth background mesh
* High bandwidth wifi p2p tunnles when needed
* Heterogeneous, many device architecture
* Fully automatic, easy, zero-configuration


##Basic Protocol Overview
Scatterbrain is a text based protocol capable of operating over many separate communication methods, including wifi p2p, bluetooth low energy, and physical dead drops. Scatterbrain aims to roughly follow the Wind concept here:[WindFarm]( https://github.com/n8fr8/WindFarm).
Particularly, Scatterbrain aims to transfer large-ish files throughout the mesh, using a semi-persistent datastore, BLE based service discovery, and high bandwidth wifi tunnels to exchange files. Filesharing services, web-like sites, or even even physical events can be advertised throughout the network. 

##Structure
Scatterbrain is very high level (basically sending raw text) in order to work over more obscure transports like BLE and static QR codes. 


##BLE Mesh Packet Types
The BLE mesh is the backbone of a Scatterbrain network. It preforms service discovery and transfer control for any higher bandwidth component. The basic unit here is a byte.
Packets are represented here with variants of `<name>(size)` Each packet has a 1 byte header with a fixed value identifying they type of packet and protocol version. The header can also be used to distinguish a Scatterbrain advertise from a normal BLE advertise. A packet can be more than 20 bytes, but has to be fragmented at a low level.  Here are the basic 'packet' units we have so far for BLE:

####Advertise
This is mainly to signal that a device is present, and to announce its capabilities. This will (hopefully, but not guaranteed) fit in a single 20 byte BLE advertise. 


`<<header>>(1)`  
`<device type>(1)`  
`<mobile status>(1)`  
`<protocol version>(2)`  
`<congestion byte>(1)`  
`<hardware services>(1)`  


Device type (integer)    
0: Android  
1: iOS  
2: Embedded GNU/Linux  

Mobile status (integer)  
0: Stationary (emplacement / server)  
1: Mobile  
2: Highly mobile  

Protocol Version (integer)  
Just here to prevent version issues.  

Congestion Byte (integer)  
Indicates how overwhelmed the sender is on a scale of 2 to 127. Basically used to help avoid clients that are in areas of congestion. 0 means manual high priority, IE the user is aggressively 'wardriving', and 1 means manual low priority, IE user is in a fast moving car and being nice to the network.     

Hardware Services  
Mask of 8 bits, each set individually to notify the receiving end what hardware the client has.   
bit 1: wifi p2p  
bit 2: wifi infrastructure client  
bit 3: wifi infrastructure AP  
bit 4: bluetooth file transfer  
bit 5: internet access  
(more bits as needed in future versions)  


####Block Data
This is a generic data packet. Also used for 'tunneled' 3rd party protocols. It can be used in response to another packet, usually as a carrier for generic mesh data or system related stuffs. Insanely general purpose. This packet uses large arbitrary sizes, and will almost always not fit on a single BLE advertise.   

```
<<Header>>(1)  


```



####Query Services
This is sent out to ask what software based services the target receiver offers (like filesharing, http-web site, etc.). This is immediately followed by a response 
