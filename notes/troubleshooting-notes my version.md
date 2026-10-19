# Troubleshooting Notes 
## 1. Issue: Capture File Location

### Observation

The original packet capture was stored in the Windows cybersecurity resources directory rather than in the new Lab 04 directory.

### Resolution

The file was checked and copied into the lab workspace:

```bash
ls -lh "/mnt/d/important docs/Cybersecurity labs and resources/quickstart.pcap"

cp "/mnt/d/important docs/Cybersecurity labs and resources/quickstart.pcap" \
  ~/zeek-labs/lab04/http-investigation.pcap
```

The copied file was then verified using:

```bash
file ~/zeek-labs/lab04/http-investigation.pcap
```

The command identified it as a PCAP version 2.4 capture using Ethernet framing.

## 2. Issue: Verifying Zeek Log Generation

### Observation

After processing the capture, it was necessary to confirm that Zeek had generated the expected HTTP and connection logs.

### Resolution

The capture was analyzed from the output directory:

```bash
cd ~/zeek-labs/lab04/zeek-output

/opt/zeek/bin/zeek -r ../http-investigation.pcap
```

The output directory was inspected:

```bash
ls -lh
```

The expected files, including `http.log` and `conn.log`, were present.

Their existence was checked explicitly:

```bash
test -f http.log && echo "http.log exists" || echo "http.log missing"

test -f conn.log && echo "conn.log exists" || echo "conn.log missing"
```

### Lesson Learned

Always verify the generated logs before running extraction commands. If an expected log is missing, investigate the capture contents and Zeek output rather than assuming the log is empty.

## 3. Issue: Unusual HTTP Method

### Observation

The HTTP method summary showed:

```text
1 GET
1 WEIRD
```

The second request contained the request line:

```text
WEIRD / HTTP/1.1
```

### Investigation

The packet was inspected using:

```bash
tcpdump -nn -A -r http-investigation.pcap 'tcp port 80'
```

The output confirmed that the unusual method token appeared in the captured HTTP request.

### Assessment

The method is unusual, but the packet does not establish the intent behind it. It may have been deliberately generated, malformed, or associated with a test. More context is required before drawing a security conclusion.

### Lesson Learned

Do not treat an unusual protocol value as proof of an attack. Confirm the underlying packet contents and document what remains unknown.

## 4. Issue: Interpreting HTTP 301 Responses

### Observation

The response-code summary showed:

```text
2 301
```

Both transactions returned `301 Moved Permanently`.

### Assessment

A 301 response indicates a permanent redirect. It does not automatically mean that the destination is malicious or that the client followed the redirect.

### Recommended Follow-up

If the response headers are available in the capture, inspect the `Location` header to identify the redirect destination. Do not infer a destination that was not observed.

## 5. Issue: Correlating HTTP and Connection Logs

### Observation

The HTTP transactions and TCP connections contained matching UIDs.

### Resolution

The HTTP `uid` field was compared with the `uid` field in `conn.log`. This enabled each recorded HTTP transaction to be associated with its corresponding network connection.

### Lesson Learned

Correlation across Zeek logs provides useful context. A connection record describes network-level behavior, while the HTTP record contains application-level details.

