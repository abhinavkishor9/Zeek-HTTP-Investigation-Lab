# Investigation Notes

## 1. Capture Validation

The original capture was copied from the Windows cybersecurity resources directory into the Lab 04 working directory.

The `file` command identified the capture as a PCAP version 2.4 file using Ethernet framing.

Zeek processed the capture successfully and generated the following logs:

- `conn.log`
- `files.log`
- `http.log`
- `packet_filter.log`
- `weird.log`

The presence of `http.log` confirmed that Zeek recorded HTTP transactions from the capture.

## 2. HTTP Transaction Analysis

The following results were extracted from `http.log`:

| Timestamp | Source | Destination | Method | Host | URI | Status | User-Agent |
|---|---|---|---|---|---|---|---|
| 1747147647.702181 | 192.168.1.8:52917 | 192.0.78.212:80 | GET | zeek.org | / | 301 | curl/8.7.1 |
| 1747147654.311012 | 192.168.1.8:52918 | 192.0.78.150:80 | WEIRD | zeek.org | / | 301 | curl/8.7.1 |

### Observations

- Two HTTP transactions were recorded.
- Both transactions used the hostname `zeek.org`.
- Both requests targeted the root URI `/`.
- The first request used the `GET` method.
- The second request used the unusual method token `WEIRD`.
- Both responses returned status code `301`, indicating a permanent redirect.
- Both transactions contained the user-agent string `curl/8.7.1`.

The timestamps correspond to the captured events, not the date when the investigation was performed.

## 3. HTTP Method Analysis

The method distribution was:

| Method | Count |
|---|---:|
| GET | 1 |
| WEIRD | 1 |

The second request requires closer inspection because `WEIRD` is not a commonly used HTTP method.

Packet-level inspection confirmed the following request line:

`WEIRD / HTTP/1.1`

The request also contained:

- `Host: zeek.org`
- `User-Agent: curl/8.7.1`
- `Accept: */*`

This confirms that the unusual token was present in the captured HTTP request. However, the capture alone does not establish why it was used or whether it was intended to test unusual HTTP parsing behavior.

## 4. HTTP Response Analysis

The response-code distribution was:

| Status Code | Count |
|---|---:|
| 301 | 2 |

Both transactions returned `301 Moved Permanently`.

A 301 response indicates that the server is redirecting the requested resource to another location. The response code alone does not establish whether the redirect destination is safe or malicious.

The available extracted output does not establish that the client followed either redirect.

## 5. Connection Correlation

The following records were extracted from `conn.log`:

| Connection UID | Source | Destination | Protocol | Service | Duration (seconds) | Origin Bytes | Responder Bytes | State |
|---|---|---|---|---|---:|---:|---:|---|
| Cgql7n1k5QtZK2oPd3 | 192.168.1.8:52917 | 192.0.78.212:80 | TCP | HTTP | 0.098478 | 71 | 377 | SF |
| C8u5cL303esmR1eOJb | 192.168.1.8:52918 | 192.0.78.150:80 | TCP | HTTP | 0.100107 | 73 | 377 | SF |

### Observations

- Two TCP connections were recorded.
- Both connections originated from `192.168.1.8`.
- Both destination ports were TCP/80.
- Each HTTP transaction can be correlated with its connection using the UID.
- The connection state `SF` is consistent with a connection that was established and finished normally in Zeek's connection-state notation.

The byte counts and connection states provide context but do not independently establish whether the activity was benign or malicious.

## 6. Evidence Assessment

### Confirmed observations

- The PCAP contains two recorded HTTP transactions.
- Both transactions target `zeek.org` and the URI `/`.
- Both responses returned HTTP 301.
- The second HTTP request contains the method token `WEIRD`.
- Both transactions use `curl/8.7.1` as the user-agent string.
- Zeek recorded corresponding TCP connection metadata.

### Unresolved questions

- Why was the unusual `WEIRD` method used?
- Was the request deliberately generated for testing, or did it originate from another activity?
- What redirect destination was returned in each response?
- Is there any related traffic outside this small capture?

These questions cannot be answered conclusively from the extracted records alone.

