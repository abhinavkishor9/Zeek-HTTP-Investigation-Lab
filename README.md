# Zeek Lab 04 — HTTP Investigation

## Overview

This lab demonstrates how to investigate HTTP traffic using Zeek and an offline packet capture (PCAP). The investigation focuses on extracting HTTP transaction details, analyzing server responses, reviewing unusual request methods, and correlating application-layer activity with connection metadata.

The lab uses an existing packet capture containing HTTP traffic associated with `zeek.org`. The analysis identifies two HTTP transactions, both returning `301 Moved Permanently`. One request uses the `GET` method, while the other contains the unusual method token `WEIRD`.

The investigation emphasizes evidence-based analysis rather than assuming that unusual traffic is malicious.

## Objectives

- Validate the PCAP format and inspect its contents before analysis.
- Generate Zeek HTTP and connection logs from an offline capture.
- Extract HTTP methods, hostnames, URIs, response codes, and user-agent strings.
- Summarize HTTP activity using `zeek-cut`, `sort`, and `uniq`.
- Correlate HTTP transactions with connection records using the Zeek connection UID.
- Investigate unusual HTTP request syntax using packet-level evidence.
- Document findings, limitations, and conclusions using SOC investigation practices.

## Lab Environment

- Operating system: Ubuntu 24.04 on WSL
- Network analysis tool: Zeek 8.0.10
- Packet inspection tool: tcpdump
- Analysis mode: Offline PCAP
- Capture file: `http-investigation.pcap`


## Investigation Workflow

1. Validate the packet capture and inspect its network traffic.
2. Process the capture with Zeek.
3. Examine `http.log` to identify HTTP requests and responses.
4. Summarize HTTP methods, hosts, URIs, and response codes.
5. Review `conn.log` to understand the associated network connections.
6. Inspect the unusual HTTP method at the packet level.
7. Document the findings and distinguish observed facts from potential concerns.

## Key Findings

The analyzed capture produced two HTTP transactions:

- Both transactions contain the hostname `zeek.org`.
- Both requests target the URI `/`.
- Both responses have status code `301` and message `Moved Permanently`.
- One request uses `GET`.
- The second request uses the unusual method token `WEIRD`.
- Both requests contain the user-agent string `curl/8.7.1`.
- The associated TCP connections originate from `192.168.1.8` and target `192.0.78.212:80` and `192.0.78.150:80`.

The packet inspection confirms that `WEIRD` appears in the second HTTP request line. This is an unusual request method token, but the available evidence does not establish whether it was intentionally generated, malformed, or associated with malicious activity.

## Evidence and Limitations

The investigation is based on a small offline packet capture. The findings describe only the traffic present in that capture.

The observed HTTP redirects do not independently indicate malicious activity. Likewise, the unusual request method requires additional context before its intent can be determined.

The capture does not establish whether the requested redirects were followed, whether the traffic was part of a larger activity chain, or whether the client or destination was compromised.

## Conclusion

This lab demonstrates how Zeek can transform captured HTTP traffic into structured logs for investigation. By combining HTTP transaction records, TCP connection metadata, and packet-level inspection, an analyst can identify unusual requests and document their significance without making unsupported threat claims.

The final assessment remains limited to the evidence available in the PCAP.
