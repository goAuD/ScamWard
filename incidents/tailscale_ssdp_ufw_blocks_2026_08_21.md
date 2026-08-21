![Status: Resolved](https://img.shields.io/badge/status-resolved-brightgreen)
![Severity: Low](https://img.shields.io/badge/severity-low-blue)
![Classification: Benign Network Discovery](https://img.shields.io/badge/classification-benign%20network%20discovery-brightgreen)
![Analyst: Goaud](https://img.shields.io/badge/analyst-goaud-black)

# Incident Report - Tailscale SSDP Discovery Responses Blocked by UFW

**Status:** Resolved / benign activity confirmed

**Incident ID:** SW-2026-0821-01

**Date:** 2026-08-21

**Timezone:** CEST (UTC+02:00)

**Category:** Network security alert / benign service discovery

**Final incident severity:** Low

> **Disclaimer:**
> This report documents a user-observed home-network security event for research
> and educational purposes. Stable device identifiers, MAC addresses, Tailscale
> addresses, and Netflix MDX values have been omitted or redacted. Private IPv4
> addresses are retained where necessary to explain the packet flow. Statements
> below distinguish direct observations from analyst inference.

## Executive Summary

A Debian server recorded repeated UFW blocks for UDP packets sent by a Nokia
Streaming Box on the same local network. The traffic used changing high-numbered
source and destination ports, initially creating the appearance of unsolicited
or unexplained communication from the streaming device to the server.

Packet payload inspection showed that the blocked packets were standard SSDP/
UPnP `HTTP/1.1 200 OK` discovery responses advertising Chromecast, DIAL, and
Netflix MDX capabilities. A subsequent bidirectional capture established that
the Debian server had first sent an SSDP `M-SEARCH` request with the broad search
target `ssdp:all` to the multicast address `239.255.255.250:1900`.

An eBPF trace correlated the 94-byte discovery request with the `tailscaled`
process. Tailscale performs local port-mapping discovery to identify UPnP IGD,
NAT-PMP, or PCP support that may improve direct peer-to-peer connectivity. The
Nokia device was not a specific target; it answered because `ssdp:all` requests
responses from all SSDP-capable devices.

No unauthorized access, exploitation, malware activity, or data transfer from
the Debian server was identified. UFW dropped the device responses before they
reached the short-lived user-space discovery socket.

## Systems and Components Involved

- Debian server at `192.168.0.213`
- Wired interface `eno1`
- UFW host firewall
- Tailscale daemon (`tailscaled`)
- Nokia Streaming Box at `192.168.0.150`
- Android 14-based Chromecast/UPnP services on the streaming box
- Local IPv4 network `192.168.0.0/24`
- SSDP multicast endpoint `239.255.255.250:1900`

The exact Debian, UFW, and Tailscale package versions were not recorded during
the investigation.

## Initial Detection

The investigation began with a kernel log entry similar to:

```text
[UFW BLOCK] IN=eno1 OUT= SRC=192.168.0.150 DST=192.168.0.213
PROTO=UDP SPT=56515 DPT=54168 LEN=806
```

Repeated packet captures showed bursts from the streaming box to one temporary
destination port on the Debian server. The destination port changed between
bursts. Eight UDP payload sizes repeatedly appeared:

```text
474, 750, 862, 798, 483, 520, 522, 792 bytes
```

The repeatable sizes indicated structured protocol responses rather than a live
media stream or random packet data.

## Detection Evidence

### Streaming device identification

The source device advertised itself through mDNS as a Nokia Streaming Box with
Google Cast support:

```text
192.168.0.150:5353 > 224.0.0.251:5353
PTR _googlecast._tcp.local
```

The stable Google Cast identifier was recorded during the investigation but is
not reproduced in this report.

### SSDP response payloads

Hex and ASCII payload inspection showed plaintext SSDP responses beginning
with:

```text
HTTP/1.1 200 OK
CACHE-CONTROL: max-age=1800
LOCATION: http://192.168.0.150:8008/ssdp/device-desc.xml
SERVER: Linux/... android14..., UPnP/1.0, Chromecast/...
```

Additional responses advertised a service endpoint on TCP port `9080` and
included the following service types:

- `upnp:rootdevice`
- `urn:dial-multiscreen-org:device:dial:1`
- `urn:dial-multiscreen-org:service:dial:1`
- `urn:schemas-upnp-org:device:mdxdevice:1`
- `urn:mdx-netflix-com:service:target:3`

Device UUIDs, friendly-name values, and the Netflix MDX link value were omitted
because they were unnecessary for root-cause determination and could provide
stable device or account correlation.

### Initiating discovery request

A capture including multicast traffic showed that the Debian server initiated
the exchange:

```text
192.168.0.213:43143 > 239.255.255.250:1900

M-SEARCH * HTTP/1.1
HOST: 239.255.255.250:1900
ST: ssdp:all
MAN: "ssdp:discover"
MX: 2
```

The high-numbered destination port in the Nokia responses matched the temporary
source port chosen for the Debian server's `M-SEARCH` request. This explained
why the target port changed between discovery attempts.

### Process attribution

The following `bpftrace` probe observed processes entering the Linux `sendto()`
system call with the known 94-byte SSDP request length:

```text
tracepoint:syscalls:sys_enter_sendto
filter: args->len == 94
```

The matching events were attributed to:

```text
process=tailscaled
```

The process attribution and packet capture were correlated by payload length,
timing, and repetition. No other matching sender was observed during the test.

## Timeline

- **2026-08-21, before 08:15 CEST:** A UFW block for UDP traffic from
  `192.168.0.150` to `192.168.0.213` was observed in the kernel log.
- **08:15 CEST:** Packet capture showed repeated UDP bursts and mDNS Google Cast
  advertisements from the source device.
- **08:23 CEST:** Bidirectional capture confirmed the streaming box sent the UDP
  bursts and performed a normal ARP refresh for the Debian server's address.
- **08:23 CEST:** Persistent listeners were reviewed. No application was
  listening on the temporary destination port, and `avahi-daemon` was inactive.
- **08:27 CEST:** Full packet payloads identified the bursts as SSDP `200 OK`
  responses for Chromecast, DIAL, and Netflix MDX services.
- **08:32 CEST:** Multicast capture identified the preceding SSDP `M-SEARCH`
  request from the Debian server with search target `ssdp:all`.
- **2026-08-21:** A targeted eBPF trace attributed matching 94-byte `sendto()`
  calls to `tailscaled`, resolving the source of the discovery request.

## Investigation Performed

### 1. Network-layer review

UFW kernel logs were parsed to establish interface, source, destination,
protocol, and port information. Ethernet and ARP captures confirmed that both
devices communicated directly on the same local layer-2 network.

### 2. Service and socket review

Persistent TCP and UDP listeners were reviewed with `ss`. The changing target
ports were not persistent application listeners. The Avahi mDNS responder was
inactive, ruling it out as the source of the SSDP request. The later process
trace explained that Tailscale used short-lived UDP sockets instead.

### 3. Payload inspection

Eight complete UDP packets were captured with link-layer and payload bytes.
Plaintext HTTP-like SSDP headers made the protocol and advertised capabilities
directly observable. Sensitive identifiers were excluded from the published
analysis.

### 4. Request-response correlation

The capture filter was expanded to include the SSDP multicast address. This
revealed the Debian-originated `M-SEARCH` request and established that the Nokia
packets were responses rather than independent probes.

### 5. Process attribution

A narrowly filtered `bpftrace` tracepoint observed 94-byte `sendto()` calls and
reported the originating command name and process ID. The sender was
`tailscaled`.

## Root Cause Assessment

### Observed

- `tailscaled` sent 94-byte network messages matching the SSDP request size.
- The Debian server transmitted `M-SEARCH` with `ST: ssdp:all` to
  `239.255.255.250:1900`.
- The Nokia Streaming Box returned multiple valid SSDP `HTTP/1.1 200 OK`
  responses to the request's temporary source port.
- UFW blocked the unicast UDP responses.
- The server did not have a persistent service listening on the destination
  ports after the short discovery window.
- Avahi was inactive and was not responsible for the request.

### Inferred

Tailscale's port-mapping component was probing the local network for a router or
gateway supporting UPnP IGD and related NAT traversal mechanisms. Because the
query used the broad `ssdp:all` search target, the Nokia Streaming Box also
responded with all of its SSDP-advertised service types.

The multicast request and unicast responses used different remote addresses
and varying source ports. This is consistent with UFW not classifying the
Nokia packets as ordinary established UDP replies and therefore applying the
default inbound block policy.

### Not confirmed

- The exact installed Tailscale version was not captured.
- Router support for UPnP IGD, NAT-PMP, or PCP was not tested.
- `tailscale netcheck` output was not collected, so the network's port-mapping
  and NAT characteristics were not documented.
- No claim is made that blocking these responses affected Tailscale direct
  connectivity; no connectivity failure was reported.

## Impact Assessment

### Confirmed impact

- Repeated UFW log entries caused concern and required investigation.
- Time was spent capturing, decoding, and attributing the traffic.
- The raw payload exposed stable local device identifiers and a Netflix MDX
  value to any administrator capturing the local traffic.

### No confirmed impact

- No unauthorized access to the Debian server was observed.
- No successful connection from the streaming box to a server application was
  identified.
- No malware, persistence, privilege escalation, or data exfiltration was
  found.
- No evidence indicated that the Nokia Streaming Box specifically targeted the
  server.
- No Tailscale outage or peer connectivity degradation was reported.

## Actions Taken

- Reviewed UFW kernel log entries.
- Captured the relevant unicast, multicast, mDNS, and ARP traffic.
- Identified the source device as a Nokia Streaming Box.
- Reviewed persistent TCP and UDP listeners.
- Confirmed that `avahi-daemon` was inactive.
- Inspected complete packet payloads and identified SSDP, Chromecast, DIAL, and
  Netflix MDX headers.
- Captured the Debian-originated `M-SEARCH` request.
- Attributed the request to `tailscaled` using an eBPF tracepoint.
- Did not create a broad UFW allow rule.
- Did not disable Tailscale's port-mapping component.

## Resolution

The activity was classified as benign network discovery. Tailscale was looking
for local port-mapping support that could assist direct peer-to-peer
connections. The Nokia Streaming Box answered the broad SSDP query as designed.
UFW blocked the responses, and no security compromise was identified.

No firewall or Tailscale configuration change was required.

## Recommended Follow-up

1. Leave the existing UFW policy unchanged unless Tailscale diagnostics show a
   specific direct-connectivity problem.
2. Run `tailscale netcheck` if port-mapping availability or DERP fallback needs
   to be evaluated. Redact public IP addresses before publishing its output.
3. Keep Tailscale updated through the trusted Debian package source.
4. Do not publish raw SSDP payloads without removing device UUIDs, friendly
   names, MAC addresses, and MDX-related values.
5. Avoid disabling Tailscale's port mapper solely to suppress benign firewall
   log entries; doing so can reduce the probability of direct connections on
   some NAT configurations.
6. If log volume becomes operationally significant, investigate narrowly scoped
   logging controls rather than broadly allowing inbound UDP from the LAN.

## Lessons Learned

- A firewall block identifies denied traffic, not necessarily hostile intent.
- Multicast discovery requests can produce unicast replies from devices that
  were never manually paired with the requesting host.
- High-numbered and changing UDP ports can be legitimate ephemeral
  request-response ports.
- Packet direction alone can be misleading if the initiating multicast request
  is excluded from the capture filter.
- Payload inspection can distinguish structured discovery traffic from opaque
  or suspicious UDP data.
- `tcpdump` establishes what was transmitted; eBPF tracing can establish which
  local process initiated the relevant system call.
- Raw discovery packets can contain persistent identifiers and should be
  minimized or redacted before publication.

## References

- [Tailscale: How NAT traversal works](https://tailscale.com/blog/how-nat-traversal-works)
- [Tailscale: Device connectivity and port mapping](https://tailscale.com/docs/reference/device-connectivity)
- [Tailscale source: portmapper client](https://github.com/tailscale/tailscale/blob/main/net/portmapper/portmapper.go)
- [Tailscale: Firewall ports and direct connections](https://tailscale.com/docs/reference/faq/firewall-ports)

## Final Status

The incident is resolved. The traffic was generated by normal Tailscale
port-mapping discovery and valid SSDP responses from a local streaming device.
No compromise, unauthorized access, or malicious activity was identified.
