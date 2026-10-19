# Zeek-HTTP-Investigation-Lab
## Overview
HTTP (Hypertext Transfer Protocol) is an application-layer protocol used by clients and web servers to exchange web requests and responses. During a security investigation, HTTP traffic can reveal which hosts communicated, what resources were requested, which methods were used, and how servers responded.

Zeek analyzes network traffic and records HTTP transactions in http.log, when the capture contains traffic Zeek can recognize as HTTP. Connection-level metadata is recorded in conn.log, which can help correlate HTTP transactions with their underlying network connections.

This lab demonstrates how to investigate HTTP traffic using Zeek and an offline packet capture (PCAP). The investigation focuses on extracting HTTP transaction details, analyzing server responses, reviewing unusual request methods, and correlating application-layer activity with connection metadata.

The lab uses an existing packet capture containing HTTP traffic associated with `zeek.org`. The analysis identifies two HTTP transactions, both returning `301 Moved Permanently`. One request uses the `GET` method, while the other contains the unusual method token `WEIRD`.

The investigation emphasizes evidence-based analysis rather than assuming that unusual traffic is malicious.

## Objectives

- Validate an offline PCAP and confirm that it contains HTTP traffic suitable for investigation.
- Analyze HTTP transactions using Zeek to identify client-server communication and requested web resources.
- Extract and interpret HTTP methods, hostnames, URIs, response status codes, status messages, and user-agent strings.
- Summarize HTTP methods and response codes to identify unusual request patterns and understand server responses.
- Investigate an unusual HTTP request method by examining the underlying packet data and validating the request line.
- Correlate HTTP transactions with TCP connection records using Zeek's connection UID.
- Examine connection metadata, including source and destination addresses, ports, connection duration, byte counts, and connection state.
- Distinguish unusual HTTP behavior from confirmed malicious activity by evaluating the available evidence.
- Document investigation findings, evidence limitations, unresolved questions, and conclusions using a structured SOC investigation approach.

## Scenario

You are working as a SOC Analyst investigating HTTP traffic captured from a network environment. Your task is to examine the recorded web requests and server responses, understand the communication between the client and external hosts, and identify any unusual behavior that may require further investigation.

You receive an offline PCAP containing HTTP traffic associated with `zeek.org`. Using Zeek, you will generate structured logs and investigate the recorded HTTP transactions. During the analysis, you identify two requests that receive `301 Moved Permanently` responses. One request uses the standard `GET` method, while the other contains an unusual method token, `WEIRD`.

The unusual request requires closer examination to determine what the packet evidence actually establishes. You must avoid assuming that an unexpected method or redirect automatically indicates malicious activity.

### Investigation Tasks

- **HTTP transaction analysis:** Extract request methods, hostnames, URIs, response codes, and user-agent strings from `http.log`.
- **Request anomaly investigation:** Examine the unusual `WEIRD` method and validate the HTTP request line against the packet contents.
- **Response analysis:** Review the HTTP 301 responses and determine what can be established about the server's redirect behavior.
- **Connection correlation:** Use Zeek connection UIDs to link HTTP transactions with their corresponding records in `conn.log`.
- **Network context:** Review source and destination addresses, ports, connection duration, byte counts, and connection states.
- **Evidence assessment:** Separate confirmed observations from potential concerns and identify questions that cannot be answered using the available capture alone.

### Expected Outcome

By completing the investigation, you should be able to analyze HTTP traffic using Zeek, correlate application-layer records with network connections, and explain the significance of unusual request behavior without overstating its security implications.

Your final assessment should clearly document the two observed HTTP transactions, their response codes, and the unusual request method. Any conclusion about malicious activity must be supported by additional evidence; otherwise, the intent behind the unusual request should remain unconfirmed.
  
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

