# Scatterbrain Protocol:  2.01
Basic protocol for https://github.com/gnu3ra/Scatterbrain.
High bandwidth internet-less 'lazy mesh' with filesharing, service discovery, and no constant connections needed. NOTE: these specs will be changed/rewritten quite a lot until I get a working prototype. Be careful.


## Overall Goals:
* Adaptable network as a base for many third party protocols
* Low bandwith, low power bluetooth background mesh
* High bandwidth wifi p2p tunnles when needed
* Heterogeneous, many device architecture
* Fully automatic, easy, zero-configuration


## Note on Bluetooth LE
It turns out that only a select few phones have the LE advertise emulation capabilities needed for Scatterbrain to work. The scatterbrain protocol is being rewritten to work with wifi direct and similar more standardized transport layers. 

## Transport protocols
The low level individual transport protocols for scatterbrain differ and use varying individual protocols. Current model will support wifi p2p and possibly bluetooth. More coming later.

## Basic Protocol Overview
Scatterbrain is a text based protocol capable of operating over many separate communication methods, including wifi p2p, bluetooth low energy, and physical dead drops. Scatterbrain aims to roughly follow the Wind concept here:[WindFarm]( https://github.com/n8fr8/WindFarm).
Particularly, Scatterbrain aims to transfer large-ish files throughout the mesh, using a semi-persistent datastore, BLE based service discovery, and high bandwidth wifi tunnels to exchange files. Filesharing services, web-like sites, or even even physical events can be advertised throughout the network. 

NOTE: This is a generic protocol with no particular use in mind. Possible uses can include text-based messaging, filesharing, news/warning systems, etc. I will make applications for Scatterbrain once I prove it works.

## Structure
Scatterbrain is very high level (basically sending raw text) in order to work over more obscure transports like BLE and static QR codes. 


## Stanza types
The scatterbrain protocol's communication is done in predefined 'stanzas'

#### Advertise
Used to announce a device and its capabilities. This may not be a physical 'packet' for some systems, as it may use an existing service discovery framework at a lower level.

header id=0(1)  
device type(1)  
mobile status(1)  
protocol version(2)  
congestion byte(1)  
hardware services(1) 
luid(6)
Device type 
0: Android  
1: iOS  
2: Embedded GNU/Linux  

Mobile status 
0: Stationary (emplacement / server)  
1: Mobile  
2: Highly mobile  

Protocol Version 
Just here to prevent version issues.  

Congestion Byte
Indicates how overwhelmed the sender is on a scale of 2 to 127. Basically used to help avoid clients that are in areas of congestion. 0 means manual high priority, IE the user is aggressively 'wardriving', and 1 means manual low priority, IE user is in a fast moving car and being nice to the network.     

Hardware Services  
Mask of 8 bits, each set individually to notify the receiving end what hardware the client has.  
bit 0: wifi p2p  
bit 1: wifi infrastructure client  
bit 2: wifi infrastructure AP  
bit 3: bluetooth file transfer  
bit 4: internet access  
(more bits as needed in future versions)  

LUID
A random string of 6 bytes, this serves as a unique identifier of a device in a local cluster or mesh. This can be randomized when not connected for privacy 


#### Block Data
This is a generic data packet, mainly used for user applications. It can be used in response to another packet, usually as a carrier for generic mesh data or system related stuffs. Insanely general purpose. This packet uses large arbitrary sizes, and will almost always not fit on a single BLE advertise. The body can be either text based, or binary. The textorbin byte is either set to 1 (text) or 0 (bin)   

header id=1(1)  
sender luid(6)  
reciever luid(6)  
textorbin(1)  
body(variable)  





#### Query Services
This stanza is used to query a device for futher information not included in a block data stanza

header id=2(1)
query mask(1)
user defined query mask(4)


Query mask:
Used to request scatterbrain specific information from a remote device. The device responds with a block data stanza. A mask of one byte.

bit 0:
Request unique id: device responds with a public key used for identifying signed messages from the device