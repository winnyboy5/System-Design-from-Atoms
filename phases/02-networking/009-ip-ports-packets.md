# 009 · IP, Ports & Packets

> ⏱ 8 min · 📈 9% · 🅰️ Part A (core) · Phase 02: Networking Essentials
>
> `█░░░░░░░░░░░░░░░░░░░` 9% of the whole guide

---

## 📖 Story

Pantry's first out-of-town order arrives from a city 300 kilometres away. Maya realizes that message hopped across dozens of machines to reach her little server. How did it know where to go? And how did it find the right program once it arrived? Time to learn the internet's addressing system.

## 🎯 One-sentence idea

**Every machine on a network has an IP address, every program on it listens on a port, and data travels in small chunks called packets that routers pass hop by hop toward the destination.**

## 🧸 Analogy

Sending mail to an **apartment building**:

- 🏢 **IP address** = the building's street address (`142.250.72.14`)
- 🚪 **Port** = the apartment number (`443` = the HTTPS apartment, `5432` = the Postgres apartment)
- ✉️ **Packets** = your long letter split into many numbered envelopes
- 📮 **Routers** = post offices that each forward the envelope one step closer

## 🖼️ Visual

```mermaid
flowchart LR
    C["💻 Your laptop<br/>192.168.1.20:52344"] --> R1["📮 Home router<br/>(NAT)"]
    R1 --> R2["📮 ISP router"]
    R2 --> R3["📮 Internet<br/>backbone"]
    R3 --> S["🖥️ Server<br/>142.250.72.14:443"]
```

**The layer cake** (simplified TCP/IP model):

```
┌──────────────────────────────┐
│ Application  HTTP, DNS, gRPC │  ← what your code speaks
├──────────────────────────────┤
│ Transport    TCP, UDP        │  ← ports live here
├──────────────────────────────┤
│ Network      IP              │  ← addresses & routing
├──────────────────────────────┤
│ Link         Ethernet, Wi-Fi │  ← the physical hop
└──────────────────────────────┘
```

## 🔬 How it works

- **IPv4:** 32-bit address like `10.0.0.5`, about **4.3 billion** addresses (not enough!). **IPv6:** 128-bit, like `2001:db8::1`, practically unlimited.
- **Public vs private IPs:** private ranges (`10.x`, `192.168.x`, `172.16–31.x`) work only inside a network. **NAT** lets many private devices share one public IP.
- **Ports (0–65535):** identify *which program* on a machine. Well-known ones: **80 HTTP, 443 HTTPS, 22 SSH, 53 DNS, 5432 Postgres, 3306 MySQL, 6379 Redis**.
- **A connection is identified by 5 things:** source IP, source port, destination IP, destination port, and protocol.
- **Packets:** data is split into chunks (about **1,500 bytes**, the MTU). Each is routed independently, and they may arrive out of order or get lost. TCP fixes that (lesson 010).
- **Routing:** each router looks at the destination IP and forwards the packet to the next hop. `traceroute` shows those hops.

## 🧩 Worked example

```bash
# Which IP does a name point to?
$ dig +short example.com
93.184.215.14

# What path do packets take?
$ traceroute example.com
 1  192.168.1.1     1 ms   (home router)
 2  10.20.0.1       8 ms   (ISP)
 ...
 9  93.184.215.14  32 ms   (destination)

# Which program is listening on which port on my server?
$ ss -ltnp
LISTEN 0.0.0.0:443   users:(("nginx"))
LISTEN 127.0.0.1:5432 users:(("postgres"))   ← only reachable from this machine
```

Notice that Postgres listens on `127.0.0.1` (localhost), so the internet can't reach it. That's a simple, common security practice.

## ⚖️ Trade-offs

| You gain | You pay | Use it when |
|---|---|---|
| Private IPs + NAT | Harder to reach devices from outside | Home networks, private cloud subnets |
| Public IPs | Exposure to the internet (must secure it) | Load balancers, public endpoints |
| IPv6 | Some legacy tooling friction | New networks, mobile, huge fleets |

## 🌍 Real world

- In the cloud (**AWS VPC**), app servers and DBs sit in **private subnets**. Only the **load balancer** has a public IP.
- **Kubernetes** gives each pod its own IP, and services get stable virtual IPs.

## 📌 Cheat card

> - **IP = building, Port = apartment, Packet = envelope, Router = post office.**
> - Ports: **80 HTTP · 443 HTTPS · 22 SSH · 53 DNS · 5432 PG · 3306 MySQL · 6379 Redis**.
> - **MTU ≈ 1,500 bytes.** IPv4 ≈ **4.3B** addresses.
> - Only expose what must be public. Keep DBs on **private IPs**.

## 🧪 Feynman check

Explain to a friend how a message from your laptop finds the right program on a server on the other side of the world. Use the apartment-building analogy.

⚠️ **Common confusion:** "An IP identifies a computer." Really, it identifies a **network interface**. One machine can have many IPs, and many machines can hide behind one IP (NAT, load balancers).

## ⚡ Quick recall

1. What's the difference between an IP and a port?
<details><summary>Answer</summary>

An IP identifies the machine/interface on the network. A port identifies the specific program/service on that machine.
</details>

2. What does NAT do?
<details><summary>Answer</summary>

It lets many devices with private IPs share one public IP by rewriting addresses and ports as traffic passes through the router.
</details>

3. Why might packets arrive out of order?
<details><summary>Answer</summary>

Each packet is routed independently and may take a different path or be delayed or retransmitted.
</details>

## 🎤 Interview practice

**Q1. "How would you lay out the network for a web app in the cloud?"**
<details><summary>Model answer</summary>

- A **VPC** with **public subnets** (only the load balancer and a bastion/NAT gateway) and **private subnets** (app servers, databases, caches).
- **Security groups/firewalls:** LB accepts 443 from the internet. App servers accept traffic only from the LB. The DB accepts only from app servers.
- Spread subnets across **multiple availability zones** for redundancy.
- Outbound internet from private subnets goes via a **NAT gateway**.
- **Likely follow-up:** "How do engineers SSH in?" → bastion host or a zero-trust access tool. Never expose SSH publicly.
</details>

**Q2. "Can a single server handle more than 65,535 connections?"**
<details><summary>Model answer</summary>

- **Yes.** A connection is identified by the 5-tuple. A server listening on port 443 can accept connections from many different client IP:port pairs, so the limit is memory and file descriptors, not ports (servers handle millions with tuning).
- The 65k limit bites on the **client side**: one client IP talking to one server IP:port can open at most about 64k connections (e.g., a proxy talking to a backend). Fix it with more source IPs or connection pooling.
- **Likely follow-up:** "What limits a server with many connections?" → memory per connection, file descriptor limits, CPU for TLS, kernel tuning.
</details>

> 📖 *Next time: The messages arrive, but should they travel like a phone call or like a postcard?*

---

⬅️ [008 · Requirements](../01-foundations/008-requirements.md) · 🗺️ [Phase map](README.md) · ➡️ [010 · TCP vs UDP](010-tcp-vs-udp.md)

✅ **Safe stopping point.** Tick lesson 009 in [PROGRESS.md](../../PROGRESS.md).
