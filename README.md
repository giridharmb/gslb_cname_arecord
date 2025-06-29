Global Load Balancing vs DNS Records

This document briefly explains the difference between DNS A Records, CNAME Records, and Global Server Load Balancing (GSLB). It also provides a simple ASCII diagram illustrating how GSLB dynamically directs traffic based on client location and health checks.

DNS A Record
  - Purpose: Directly maps a hostname to one or more IPv4 addresses.
  - Usage:

www.example.com.  IN A   192.0.2.10


  - Characteristics:
  - Simple and static.
  - Every query returns the same IP address(es).

DNS CNAME (Canonical Name) Record
  - Purpose: Creates an alias from one hostname to another hostname (not an IP).
  - Usage:

shop.example.com.    IN CNAME   store-backend.example.com.
store-backend.example.com. IN A   192.0.2.20


  - Characteristics:
  - Enables flexible naming and delegation.
  - Still static: the alias always resolves to the same target’s A/AAAA records.

Global Server Load Balancer (GSLB)
  - Purpose: Provides geographic and health-aware DNS responses to distribute traffic across multiple data centers.
  - How it works:
  - The authoritative DNS server is managed by the GSLB engine.
  - Based on the client’s location, site health, or load, it returns different IP addresses.
  - Supports automatic fail-over: if one site is down, GSLB will stop returning its IP.

Sample GSLB Flow Diagram

```
           [ User in Europe ]              [ User in US ]

                   |                              |
        +----------v----------+         +---------v----------+
        |     Recursive DNS   |         |    Recursive DNS   |
        +----------+----------+         +---------+----------+
                   |                              |
      +------------v------------------+------------v-------------+
      |            GSLB DNS server (global)                      |
      | ————————————dynamic A/CNAME response———————————--------- | 
      |                                                          |
      |  if client geo=Europe → return  203.0.113.10             |
      |  if client geo=US     → return  198.51.100.20            |
      +----------------------------------------------------------+
                   |                              |
        +----------v----------+         +---------v-----------+
        |  Web Server EU (DC1)|         |  Web Server US (DC2)|
        |  IP = 203.0.113.10  |         |   IP = 198.51.100.20|
        +---------------------+         +---------------------+
```

Key Differences

Feature	A Record	CNAME Record	GSLB
Mapping	Hostname → IP	Hostname → Hostname	Hostname → Dynamic IP(s) based on logic
Flexibility	Low (static)	Medium (aliasing)	High (geolocation, health checks, traffic steering)
Fail-over	No	No	Yes (automatic site health-based failover)
Traffic Distribution	N/A	N/A	Yes (round-robin, weighted, geo-based, etc.)

When to Use
  - A Record: Simple setups with a single server or static IPs.
  - CNAME Record: When you need aliasing (e.g., multiple hostnames pointing to the same service) but no advanced load balancing.
  - GSLB: For global applications requiring high availability, geographic performance optimization, and automatic failover across multiple data centers.