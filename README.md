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


### Add Best Practice Anti-Spyware Profiles

Found in Objects > Security Profiles > Anti-Spyware 

The skillet creates 2 Anti-Spyware profiles. The first is configured so that the DNS policies protect your network from
DNS queries to malicious domains. The policy action is set to sinkhole for DNS signature sources which identifies potentially
compromised hosts that attempt to access suspicious domains and preventing them from accessing those domains. PANW recommends
using the **sinkhole** policy action instead of **block** to maintain optimum protection while providing a mechanism to assist
in identifying compromised endpoints. We use packet capture for domains that pose a greater threat so that we can determine if
the attack was successful, identify the attack methods and provide better overall context.

The second Anti-Spyware profile has a signature policy that is simply configured to block all possibly spyware with severity
levels of **low** and higher. The action performed by this profile is to reset-both and packet capturing is disabled.


### Add File Blocking Profile for PE

Found in Objects > Security Profiles > File Blocking

File Blocking allows you to identify specific file types that you want to block or monitor, for most traffic block files that
are known to carry threats or that have no real use case for upload/download. This profile blocks Portable Executable(PE) file
types for internet-based SMB traffic from traversing trust to untrust zones.


### Add Best Practice Antivirus Profiles

Found in Objects > Security Profiles > Antivirus

The skillet creates 2 Antivirus profiles. The first AV profile is configured to take advantage of Wildfire inline ML, but keep
in mind you must have an active Wildfire subscription to do this. Wildfire inline ML allows you to dynamically analyze files
using ML on the firewall. We use Wildfire inline ML to block malicious variants of PE(portable Executables), PowerShell
scripts, and ELF files in real-time. This additional layer of antivirus protection complements the WildFire-based signatures 
to provide extended coverage for files of which signatures do not already exist.

The second AV profile is configured to block all content that matches an antivirus signature. For all relevant categories it
sets the signature action, Wildfire signature action and Wildfire inline ML action to **reset-both**.


### Add Zone Protection Profile

Found in Network > Network Profiles > Zone Protection

This zone protection profile is configured to protect against packed-based attacks. It is configured to drop malformed
IP packets and mismatched overlapping TCP segment options. Dropping the mismatched overlapping TCP segments option specifies
that PAN-OS discards frames with mismatched and overlapping data. This prevents attackers from causing misinterpretations in
the intent of the connection and deliberately induce false positives or false negatives.


### Add Vulnerability Protection Profile

Found in Objects > Security Profiles > Vulnerability Profiles

This vulnerability protection profile blocks all protocol anomalies and all vulnerabilities with low and high severities.
Protocol anomalies occur when a protocol behavior devieates from standard and acompliant usage. The rules are listed with
blocking protocol anomalies first and then blocking all other vulnerabilities underneath. traffic is evaluated against
security policy rules and associated vulnerability protection profiles rules from top to bottom and is enforced based
ont the first matching rule.
