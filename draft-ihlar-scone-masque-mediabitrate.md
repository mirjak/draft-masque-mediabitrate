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

normative:

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
  Length (i)
  Rate Limit (i)
  [Average Window (i)]
}
~~~

The capsule has the following fields:

Rate Limit: The maximum sustainable throughput that the client can expect for proxied traffic,
expressed in kilobits per second.

Average Window: Indicates the duration over which the bitrate is enforced, expressed in milliseconds.
This field is optional.

# Applicability

A proxy that intends to rate limit proxied traffic can notify clients using the
THROUGHPUT_ADVICE capsule. Reasons for rate limiting traffic through a proxy
include enforcement of access network policies, proxy resource management and
proxy service differentiation.

If the sole purpose of the communication between a client endpoint and a network element
is the exchange of throughput advice, it is RECOMMENDED to use more lightweight approaches
than HTTP proxying, such as {{?TRONE=I-D.thoji-scone-trone-protocol}}.

# Security Considerations

TODO Security


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

TODO acknowledge.
