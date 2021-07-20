# Best Practices Quickplay Solution


## Prerequisites 
The following should be completed before running the Best Practices Quickplay Solution:

    1. Deploy a PAN-OS 10.X NGFW
    2. License NGFW with the Threat, URL, and WildFire subscriptions
    3. Install PanHandler (Version 4.5+) or other skillet-supported applications, such as SLI
    4. Configure the NGFW for IronSkillet

> **NOTE**: You must have IronSkillet configured on your NGFW before loading the Best Practices solution 
> since many Best Practices configuration elements depend on IronSkillet elements.

## Submodules Utilized

This quickplay solution uses the [panos-best-practices](https://gitlab.com/panw-gse/as/panos-best-practices) 
repository as a submodule.

## Internet Gateway Skillet Details

### Add monitoring reports

Assists with monitoring logs to find policy gaps and abnormal behavior.

### Best Practice Security Profiles 

Adds best practice Antivirus, Anti-Spyware, Vulnerability, Wildfire, URL Filtering, File Blocking
security profiles. 

> **NOTE**: These profiles will need to be added manually to security policies during policy tuning.

### Best Practice Security Policies based on Trusted Threat Intelligence Sources

 - Block Inbound/Outbound known malicious IP addresses
 - Block Inbound/Outbound bulletproof IP addresses
 - Block Inbound/Outbound high-risk IP addresses

## Layer 4 - Layer 7 Skillet Details


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
