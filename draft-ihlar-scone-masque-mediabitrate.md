---
title: "MASQUE extension for signaling throughput advice"
abbrev: "MASQUE throughput advice capsule"
category: std

docname: draft-ihlar-scone-masque-mediabitrate-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Web and Internet Transport"
workgroup: "Standard Communication with Network Elements"
keyword:
 - media bitrate
venue:
  group: "Standard Communication with Network Elements"
  type: "Working Group"
  mail: "scone@ietf.org"
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
 -
    fullname: Zaheduzzaman Sarker
    organization: Nokia
    email: zaheduzzaman.sarker@nokia.com

normative:
  SCONE: I-D.ietf-scone-protocol
  QUIC-PROXY: I-D.ietf-masque-quic-proxy

informative:


--- abstract

This document specifies a new Capsule (RFC9297) that can be used with CONNECT-UDP (RFC9298), CONNECT-IP (RFC9484), or other future CONNECT extensions to signal throughput advice for
traffic that is proxied through an HTTP server.

--- middle

# Introduction

This document specifies an HTTP Capsule (RFC9297) that can be used with CONNECT-UDP (RFC9298), CONNECT-IP (RFC9484),
or other future CONNECT extensions to signal throughput advice for traffic proxied through an HTTP server.

The extension can be used with the HTTP CONNECT method when the :protocol pseudo-header is equal to "connect-udp"
or "connect-ip", as well as with future CONNECT protocols that use the Capsule Protocol.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Indicating Support for Throughput Advice Signaling

A client that wishes to receive throughput advice capsules can indicate support by sending a request header
with the boolean-valued Item Structured Field: "Throughput-Advice: ?1". The HTTP proxy can indicate support
by sending a response header with the same boolean-valued Item Structured Field: "Throughput-Advice: ?1".
See {{Section 3.3.6 of !RFC8941}} for information about the boolean format.

Once support has been established, a proxy MAY send THROUGHPUT_ADVICE capsules at any time during the
lifetime of the stream that originated the request.

# THROUGHPUT_ADVICE Capsule Type Format

The THROUGHPUT_ADVICE Capsule has the following format:

~~~
THROUGHPUT_ADVICE Capsule {
  Type (i) = 0xTBD,
  Length (i),
  Direction (8),
  Rate Limit (i),
  [Average Window (i)]
}
~~~

The capsule has the following fields:

Direction: Indicates the traffic direction to which this throughput advice applies.
Valid values are:

* 0x00: Both uplink and downlink
* 0x01: Uplink (client to target)
* 0x02: Downlink (target to client)

A client MUST treat any other value as a malformed capsule.

Rate Limit: The maximum sustainable throughput that the client can expect for proxied traffic,
expressed in kilobits per second.

Average Window: Indicates the duration over which the bitrate is enforced, expressed in milliseconds.
If this field is omitted the average window is assumed to be 67 seconds as described in {{Section 5.2 of SCONE}}.

# Relationship to the SCONE Protocol

This document reuses the SCONE {{SCONE}} conceptual model for throughput
advice but scopes signaling to the HTTP tunnel between a MASQUE client and a
MASQUE server. When the Throughput-Advice header is successfully
negotiated, the MASQUE server is the entity that originates
THROUGHPUT_ADVICE capsules toward the client; the client does not send
capsules unless specified by future extensions.

Implementations that negotiate
Throughput-Advice for a MASQUE tunnel SHOULD NOT initiate or forward
SCONE packets on the outer MASQUE connection for the purpose of conveying
throughput advice.

When a MASQUE proxy observes SCONE packets that belong to an end-to-end
inner flow carried by the tunnel, the proxy MUST forward those packets unmodified.

## Interaction with QUIC-Aware Forwarding

When used in combination with QUIC-Aware Forwarding
{{QUIC-PROXY}}, QUIC long-header
packets are tunnelled rather than being forwarded forwarded directly.
Since SCONE packets use a dedicated QUIC version and the long-header format,
they will be encapsulated automatically inside the MASQUE tunnel.

# Applicability

A proxy that intends to rate limit proxied traffic can notify clients using the
THROUGHPUT_ADVICE capsule. Reasons for rate limiting traffic through a proxy
include enforcement of access network policies, proxy resource management and
proxy service differentiation.

If the sole purpose of the communication between a client endpoint and a network element
is the exchange of throughput advice, it is RECOMMENDED to use more lightweight approaches
than HTTP proxying, such as {{SCONE}}.

## Applicability to Proxied Applications

In most MASQUE deployments, the client that terminates the HTTP tunnel is not
the ultimate endpoint of the application traffic. Throughput advice therefore
applies to the aggregate traffic carried by the tunnel rather than to any
individual application flow.

How a MASQUE client exposes throughput advice to the applications that use the
tunnel is out of scope for this document. Implementations may, for example:

* Use the advice to apply back-pressure on proxied traffic;
* Forward the information through an out-of-band API or control channel; or
* Adjust sending behavior on behalf of the application.

For CONNECT-UDP requests, the advice typically corresponds to the throughput
of a single proxied flow, whereas for CONNECT-IP requests it applies to the
aggregate traffic within the tunnel.

# Security Considerations

Throughput advice influences application sending behavior and can therefore
affect performance and user experience. Implementations MUST treat such
signals as advisory information.
A malicious or misconfigured proxy could advertise unrealistically low rate
limits to degrade service quality or influence path selection and traffic
distribution. Clients MAY ignore any received advice.

When QUIC-Aware Forwarding is in use, SCONE
packets are encapsulated as QUIC long-header packets and therefore not
visible to on-path observers. This encapsulation is RECOMMENDED since it
prevents correlation between throughput-advice signaling and proxied
application traffic.


# IANA Considerations

## Capsule types

This document adds following entries to the "HTTP Capsule Types" registry:

| Capsule Type       | Value | Specification   |
| ------------------ | ----- | --------------- |
| THROUGHPUT_ADVICE  | TBD   | (This document) |
{: #iana-capsule-type title="New Capsule Type to register" cols="l l l"}

## HTTP headers

This document adds following entry to the "Hypertext Transfer Protocol (HTTP) Field Name Registry":

| Field Name     | Template | Status    | Reference       | Comments |
| -------------- | -------- | --------- | --------------- | -------- |
| Throughput-Advice  |          | permanent | (This document) |          |
{: #iana-http-field title="HTTP Field Name to register" cols="l l l l l"}

--- back

# Acknowledgments
{:numbered="false"}

Zaheduzzaman Sarker have provided significant comments and feedback
that has helped shape the draft.
