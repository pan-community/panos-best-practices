# PAN-OS Best Practices

Best Practice configs that add on to HomeSkillet/IronSkillet


#Internet Gateway Add-On Skillet
content coming soon.

# Layer 4 to 7 Networking Security Add-On Skillet

This skillet is meant as an add-on to the base HomeSkillet/IronSkillet configurations.
Please load in HomeSkillet with IronSkillet as a baseline prior to loading this 
configuration skillet to your NGFW.

## What this Skillet does


### Enable drop Mismatched Overlapping TCP Segment

Found in: Network > Network Profiles > Zone Protection > Packet Based Attack Protection

Selecting the `Mismatched overlapping TCP segment` option specifies that PAN-OS
discards frames with mismatched and overlapping data. Received segments are
discarded when they are contained with another segment, when they overlap with
part of another segment, or when they contain another complete segment.

Selecting this option is important because attackers will deliberately construct connections
with overlapping but different data within them, they try to cause misinterpretation 
of the intent of the connection and deliberately induce false positives or false negatives.
Attackers will also use IP spoofing and sequence number prediction to intercept a user's 
connection and inject their own data into that connection.


### Enable Multicast Support

Found in: Network > Virtual Router > Multicast

Enable support for multicast traffic so that the firewall can enforce policy on
multicast traffic


### Disable Allow HTTP Partial Response

Found in: Device > Setup > Content-ID > Content-ID Settings

The HTTP partial response option allows a client to fetch only part of a file. When 
a next-generation firewall in the path of a transfer identifies and drops a malicious
file it terminates the TCP session with an RST packet. If the web browser implements
the HTTP header range option, it can start a new session to fetch only the remaining 
part of the file, which prevents the firewall from triggering the same signature again 
due to the lack of context into the initial session. At the same time, this allows the
web browser to reassemble the file and deliver the malicious content. Disabling
this option prevents this from happening.