Odoo + FreePBX VoIP Integration Guide
Overview
This document describes how to configure Odoo VoIP to work with FreePBX using WebRTC.

Architecture:

Odoo Browser (WebRTC)

        ↓

FreePBX (Asterisk PJSIP)

        ↓

SIP Trunk Provider

        ↓

Public Phone Network

For stability, two extensions are recommended per user:

Extension	Purpose
2000	Odoo WebRTC
2001	Desk Phone / Softphone
1. FreePBX Configuration
1.1 Create WebRTC Extension for Odoo
Navigate to:

Applications → Extensions → Add Extension → PJSIP

Example configuration:

Field	Value
Extension	2000
Display Name	Odoo
Transport	WSS
WebRTC	Yes
1.2 Advanced Extension Settings
Important settings for WebRTC:

Setting	Value
AVPF	Yes
ICE Support	Yes
RTP Symmetric	Yes
Rewrite Contact	Yes
Force rport	Yes
Direct Media	No
Media Encryption	DTLS-SRTP
These settings ensure proper browser media negotiation.

1.3 Allowed Codecs
Recommended codecs:

ulaw

alaw

opus

Order priority:

ulaw

alaw

opus

Explanation:

Codec	Purpose
ulaw	PSTN trunks
alaw	European trunks
opus	WebRTC browsers
2. FreePBX SIP Settings
Navigate to:

Settings → Asterisk SIP Settings

NAT Settings
Setting	Value
External Address	Public IP of PBX
Local Networks	Your LAN subnet
Example:

External Address: XXX.XXX.XXX.XXX

Local Networks: XXX.XXX.XXX.XXX/XX

RTP Settings
RTP Port Range

Start: 10000

End: 20000

Audio Codecs
Enable:

ulaw

alaw

opus

g722

Disable unnecessary codecs to reduce negotiation errors.

3. TLS / WebRTC Configuration
Ensure TLS is enabled in the extension.

Settings:

Setting	Value
Enable DTLS	Yes
Certificate	hostname (host.example.com)
DTLS Verify	Fingerprint
The PBX must have a valid certificate for the domain used by Odoo.

4. Firewall Requirements
Open the following ports:

Port	Protocol	Purpose
5060	UDP	SIP
8089	TCP	WebRTC WSS
10000-20000	UDP	RTP Audio
If using the FreePBX firewall module, enable SIP WebSocket service.

5. Odoo Configuration
Navigate to:

Settings → VoIP → Providers

Create provider:

Field	Value
Name	FreePBX
Domain	pbx.datalabsystems.com
WebSocket URL	wss://host.example.com:8089/ws
SIP Proxy	host.example.com
Configure User
Navigate to:

Settings → Users → VoIP

Example:

Field	Value
SIP Login	2000
SIP Password	Extension secret
VoIP Provider	FreePBX
6. Testing
Echo Test
Dial from Odoo:

*43

Expected result: echo audio playback.

Outbound Call
Dial a full number such as:

XXXXXXXXXX

7. Troubleshooting
Calls drop after answer
Check:

Allowed codecs

DTLS enabled

ICE enabled

No audio
Verify:

RTP ports open

External address configured

WebRTC does not register
Check:

8089 WSS port open

Valid TLS certificate

8. Recommended Deployment Model
Example extension allocation:

User	Odoo	Desk Phone
user	2000	2001
Support	2010	2011
Sales	2020	2021
This prevents SIP/WebRTC conflicts.
