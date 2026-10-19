# Timeline — Zeek HTTP Investigation

## Investigation Timeline

The following timeline records the two HTTP transactions observed in the packet capture. The timestamps are derived from Zeek's logs and reflect the captured events, not the date the lab was performed.

| Timestamp (Unix) | Connection UID | Source | Destination | Observed Activity | Result |
|---|---|---|---|---|---|
| 1747147647.668533 | Cgql7n1k5QtZK2oPd3 | 192.168.1.8:52917 | 192.0.78.212:80 | TCP connection recorded | Connection state `SF` |
| 1747147647.702181 | Cgql7n1k5QtZK2oPd3 | 192.168.1.8:52917 | 192.0.78.212:80 | HTTP `GET /` request to `zeek.org` | HTTP 301 Moved Permanently |
| 1747147654.275660 | C8u5cL303esmR1eOJb | 192.168.1.8:52918 | 192.0.78.150:80 | TCP connection recorded | Connection state `SF` |
| 1747147654.311012 | C8u5cL303esmR1eOJb | 192.168.1.8:52918 | 192.0.78.150:80 | HTTP `WEIRD / HTTP/1.1` request to `zeek.org` | HTTP 301 Moved Permanently |

## Timeline Interpretation

### Event 1 — Standard HTTP Request

At timestamp `1747147647.702181`, the client at `192.168.1.8` sent a `GET` request for `/` with the hostname `zeek.org`.

The server at `192.0.78.212:80` returned a `301 Moved Permanently` response. Zeek recorded the transaction and its corresponding TCP connection.

### Event 2 — Unusual HTTP Request Method

At timestamp `1747147654.311012`, the same client sent a request containing the method token `WEIRD` to the same hostname, using a separate TCP connection to `192.0.78.150:80`.

The server returned a `301 Moved Permanently` response. Packet-level inspection confirmed that the request line contained `WEIRD / HTTP/1.1`.

The unusual method warrants documentation and further context, but the observed response does not establish malicious intent.

## Overall Assessment

The capture records two HTTP transactions approximately 6.6 seconds apart. Both target `zeek.org`, request `/`, and receive HTTP 301 responses. One uses the standard `GET` method, while the other uses the unusual token `WEIRD`.

The available evidence confirms the observed request and response behavior. It does not establish the purpose of the unusual request, the safety of the redirect destinations, or whether either transaction was part of a broader security incident.

**Conclusion:** Two HTTP transactions observed; one unusual request method identified; malicious activity not established.
