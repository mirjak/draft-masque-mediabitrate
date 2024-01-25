---
title: "MASQUE extension for signaling media bitrate"
abbrev: "MASQUE media bitrate capsule"
category: std

docname: draft-ihlar-masque-mediabitrate-latest
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

This document specified a new Capsule (RFC9297) that can be used with CONNECT-UDP (RFC9298), CONNECT-IP (RFC9484), or other future CONNECT extensions to signal the available bitrate from a HTTP proxy in the network to the HTTP client.
This information can be used by application server to self-regulate it sending rate in order to impacts by in-network traffic shaping.


--- middle

# Introduction

This document specified a new Capsule {{!RFC9297}} that can be used with CONNECT-UDP {{!RFC9298}}, CONNECT-IP {{!RFC9484}}, or other future CONNECT extensions to signal a maximum available bitrate from a HTTP proxy in the network to the HTTP client.
This information can be used by application server to self-regulate it sending rate in order to impacts by in-network traffic shaping.

The extension can be used with the HTTP CONNECT method when the :protocol pseudo header is equal to "connect-udp" or "connect-ip" as well as with future CONNECT protocols that use the Capsule Protocol.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Requesting media bitrate signaling

Clinets can request to received media bitrate information from an
HTTP proxy by including the boolean-valued Item Structured Field "Media-Bitrate: ?1" in the HTTP Request.
The HTTP proxy indicates support by also adding "Media-Bitrate: ?1" in the Response headers.
See {{Section 3.3.6 of !RFC8941}} for information about the boolean format.

If media bitrate signaling is requested by the client and suported by the proxy,
the proxy MAY send an MEDIA_BITRATE capsule any time during the lifetime of the stream
where media bitrate signaling was requested.

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
