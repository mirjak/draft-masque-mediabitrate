---
title: "MASQUE extension for signaling media bitrate"
abbrev: "MASQUE media bitrate capsule"
category: std

docname: draft-ihlar-masque-sconepro-mediabitrate-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Web and Internet Transport"
workgroup: "Multiplexed Application Substrate over QUIC Encryption"
keyword:
 - media bitrate
venue:
  group: "Multiplexed Application Substrate over QUIC Encryption"
  type: "Working Group"
  mail: "masque@ietf.org"
  github: "mirjak/draft-masque-mediabitrate"

author:
 -
    fullname: Marcus Ihlar
    organization: Ericsson
    email: marcus.ihlar@ericsson.com
 -
    fullname: Mirja Kühlewind
    organization: Ericsson
    email: mirja.kuehlewind@ericsson.com

normative:

informative:


--- abstract

This document specifies a new Capsule (RFC9297) that can be used with CONNECT-UDP (RFC9298), CONNECT-IP (RFC9484), or other future CONNECT extensions to signal the available
bitrate for media traffic that is proxied through an HTTP server.
This information can be used by a media application to regulate it's media bitrate in accordance with a network policy, as an alternative to in-network traffic shaping.


--- middle

# Introduction

This document specifies a new Capsule (RFC9297) that can be used with CONNECT-UDP (RFC9298), CONNECT-IP (RFC9484), or other future CONNECT extensions to signal the available
bitrate for media traffic that is proxied through an HTTP server.
This information can be used by a media application to regulate it's media bitrate in accordance with a network policy, as an alternative to in-network traffic shaping.

The extension can be used with the HTTP CONNECT method when the :protocol pseudo header is equal to "connect-udp" or "connect-ip" and with future CONNECT protocols that use the Capsule Protocol.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Indicating Support for Media Bitrate Signaling

A client who wishes to receive media bitrate capsules can indicate support by sending a request header with
the boolean-valued Item Structured Field "Media-Bitrate: ?1".
The HTTP proxy indicates support by sending a response header with the boolean-valued Item Structured Field "Media-Bitrate: ?1"
See {{Section 3.3.6 of !RFC8941}} for information about the boolean format.

Once support has been established, a proxy MAY send MEDIA_BITRATE capsules at any time during the lifetime of the stream that originated the request.

# MEDIA_BITRATE Capsule Type Format

The MEDIA_BITRATE Capsule has the following format:

~~~
MEDIA_BITRATE Capsule {
  Type (i) = MEDIA_BITRATE,
  Length (i)
  Media Bitrate (i)
  [Average Window (i)]
}
~~~

The capsule has the following fields:

Media Bitrate: Indicates the average bitrate that is supported by the network without traffic shaping.

Average Window: Indicates the duration over which the bitrate is enforced. The largest allowed burst is given by Media Bitrate * Average window. This field is optional.

# Client Behaviour

A client that receives media bitrate capsules needs to make the information available to the media application, this is implementation specific and out of scope for this document.
A media application can use the information to select a media track that conforms with the specified bitrate. How this is done and whether an application client needs to explicitly
coordinate with an application server is out of scope for this document.

# Proxy Behaviour

A proxy that sends media bitrate capsules needs to be tightly integrated with the access network infrastructure and policy framework. A proxy that sends media bitrate capsules does
so as an alternatvie to traffic shaping, the policies that govern shaping behaviour may be used to determine the values sent with media bitrate capsules.
A proxy may wish to enforce that the bitrate policy is respected and apply shaping or policing to traffic that is breaching the policy. It is RECOMMENDED that the proxy uses an
averageing window that is sufficiently long to allow data transmission bursts that make full use of the available network capacity. A proxy can use the Average Window field in the
media bitrate capsule to inform the client about how it enforces bitrates.

# Performance Considerations

This protocol is intended to provide policy indications to applications traversing a network. Using HTTP proxying for this purpose does add overhead in terms of CPU, memory and MTU.
It is RECOMMENDED that this solution is used together with QUIC-Aware proxying {{!I-D.ietf-masque-quic-proxy}} whenever possible.

# Security Considerations

TODO Security


# IANA Considerations

## Capsule types

This document adds following entries to the "HTTP Capsule Types" registry:

| Capsule Type   | Value | Specification   |
| -------------- | ----- | --------------- |
| MEDIA-BITRATE  | TBD   | (This document) |
{: #iana-capsule-type title="New Capsule Type to register" cols="l l l"}

## HTTP headers

This document adds following entry to the "Hypertext Transfer Protocol (HTTP) Field Name Registry":

| Field Name     | Template | Status    | Reference       | Comments |
| -------------- | -------- | --------- | --------------- | -------- |
| Media-Bitrate  |          | permanent | (This document) |          |
{: #iana-http-field title="HTTP Field Name to register" cols="l l l l l"}

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
