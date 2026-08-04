ok, so I am learning a network and RHEL. I want to create my clear notes, based on these tips.

If you're studying subjects like **RHEL, Linux, networking, security, scripting, and systems administration**, the goal of notes isn't to create a textbook—it's to create something you'll actually use when troubleshooting or reviewing months later.

A useful rule is:

> **Your notes should answer questions, not just record information.**

Instead of writing:

> TCP uses a three-way handshake.

Write:

> **How does a TCP connection start?**
>
> 1. Client → SYN
> 2. Server → SYN-ACK
> 3. Client → ACK
>
> **Why?**
>
> * Confirms both sides can send/receive.
>
> **Troubleshooting**
>
> * SYN only → firewall?
> * SYN-ACK missing → server unreachable?

That small change makes review much faster.

---

# A good note structure

I like using a consistent template for every topic.

```text
# Topic

## What is it?

One paragraph.

## Why does it exist?

The problem it solves.

## How does it work?

Simple explanation.

## Commands

Most useful commands.

## Diagram

ASCII or hand drawing.

## Example

Real example.

## Common mistakes

Things people confuse.

## Exam points

Things likely to be asked.

## Real-world usage

Where you'll actually encounter it.
```

---

# Example: RHEL Systemd

```text
SYSTEMD

What?
-------
Init system that starts and manages services.

Why?
-------
Replaced SysV init.
Starts services in parallel.
Tracks dependencies.

Common Commands
----------------

systemctl status sshd

systemctl start sshd

systemctl stop sshd

systemctl restart sshd

systemctl enable sshd

systemctl disable sshd

systemctl list-units

Flow
------

Boot

↓

systemd

↓

target

↓

services

↓

multi-user.target

↓

sshd
firewalld
NetworkManager

Common Problems
----------------

Service won't start

↓

systemctl status

↓

journalctl -xe

↓

Fix config

↓

restart service
```

Notice there isn't much text.

Mostly structure.

---

# Networking example

Instead of writing pages...

Write something visual.

```
Client
192.168.1.20

|

|

Switch

|

|

Router
192.168.1.1

|

|

Internet

```

Then next to it

```
Ping

↓

ARP

↓

MAC learned

↓

ICMP Echo

↓

Reply
```

Instant understanding.

---

# Use colors consistently

For handwritten notes:

**Blue**

* Definitions

**Black**

* Normal text

**Red**

* Warnings
* Important

**Green**

* Commands
* Configuration

Example

```
RED
--------
Never edit /etc/passwd manually

GREEN
---------
vipw

BLACK
---------
Safely edits passwd file.
```

---

# Build cheat sheets

Have one page for each topic.

Example:

## Permissions

```
chmod

r = 4
w = 2
x = 1

755

Owner
7 rwx

Group
5 r-x

Other
5 r-x
```

Everything on one page.

---

# Make flowcharts

Networking especially benefits from flows.

Example

```
User types

google.com

↓

DNS lookup

↓

IP returned

↓

TCP handshake

↓

TLS

↓

HTTP GET

↓

Server Response
```

Much easier than paragraphs.

---

# Compare things

Tables are excellent.

| Command | Purpose    | Common Option |
| ------- | ---------- | ------------- |
| `ls`    | List files | `-l`          |
| `cp`    | Copy       | `-r`          |
| `mv`    | Move       | `-i`          |
| `rm`    | Delete     | `-r`, `-f`    |

---

# Include mistakes

This is often the highest-value section.

Example:

```
Common mistakes

❌ chmod 777 everything

Instead

✓ Use least privilege

❌ kill -9 immediately

Instead

✓ Try normal kill first

❌ Disable SELinux first

Instead

✓ Check audit logs
```

You'll remember these much better because they mirror real troubleshooting.

---

# Use "question pages"

One page only has questions.

Example:

```
Can I explain:

□ What is ARP?

□ Difference between TCP and UDP?

□ How routing works?

□ What does DHCP actually do?

□ What is a VLAN?

□ What happens after typing google.com?
```

If you cannot answer one from memory, that's the topic to review.

---

# Leave space

Don't fill every inch of the page.

Leave margins for:

* extra commands
* exam tips
* corrections
* examples
* new discoveries

Good notes evolve as you learn.

---

# Draw more than you write

For Linux:

```
Filesystem

/

├── home

├── etc

├── var

├── usr

├── boot

└── tmp
```

For networking

```
PC

↓

Switch

↓

Router

↓

Firewall

↓

Internet
```

Simple diagrams are often more memorable than paragraphs.

---

# Have a "real commands" section

After every concept, include commands you've actually run.

Example

```
hostnamectl

ip addr

ip route

ss -tulpn

journalctl -u sshd

systemctl status sshd

nmcli connection show
```

This connects theory to practice.

---

# End every topic with a summary

A small box like:

```
Remember

✓ What it is

✓ Why it exists

✓ Main command

✓ One real example

✓ One troubleshooting step
```

If you can explain those five points without looking, you probably understand the topic well.

---

## An example one-page layout

```
=================================================

TCP

Definition
-----------
Reliable transport protocol.

Diagram
-----------

Client          Server

SYN  --------->

     <--------- SYN ACK

ACK  --------->

Commands
-----------

ss -t

netstat

tcpdump

Ports
---------

HTTP     80

HTTPS    443

SSH       22

Problems
------------

Connection refused

↓

Service not running

↓

Firewall

↓

Wrong port

Remember
------------

Reliable

Ordered

Acknowledgements

Retransmission

Flow control

=================================================
```

This kind of page is quick to scan before a lab or exam.

For technical subjects like RHEL and networking, many experienced engineers find that a **hybrid approach** works best: maintain a clean digital knowledge base (for searchable explanations, commands, and links), and keep handwritten notes for diagrams, troubleshooting flows, and memory aids. The handwritten pages reinforce understanding, while the digital notes become your long-term reference. Over time, you'll end up with something that feels more like your own systems administration handbook than a collection of copied lecture notes.


The short answer is: **use both, but for different purposes.** Research and practical experience suggest each has strengths.

### Handwriting is better for learning

When you write by hand, you're forced to summarize and reorganize information instead of copying it verbatim. That deeper processing tends to improve understanding and long-term memory.

It's especially useful for:

* Networking diagrams (topologies, packet flow)
* Linux boot process
* Filesystem trees
* Mind maps
* Flowcharts
* Exam preparation
* Explaining concepts in your own words

For example, a handwritten page on TCP might include a simple three-way handshake diagram, a note about why each step exists, and a few troubleshooting clues.

---

### Typing is better for building a knowledge base

Digital notes excel when you need to:

* Search instantly
* Update information
* Copy commands
* Add screenshots
* Link related topics
* Access notes from multiple devices

For technical subjects like RHEL, your notes will grow over time. A typed note is much easier to maintain than rewriting an entire notebook because a command or recommendation changed.

For example, a digital note on SELinux can include:

* Common commands
* Examples
* Configuration snippets
* Links to related topics like systemd, firewalld, or file permissions

---

## For Linux and networking, I'd recommend this workflow

### 1. Learn by hand

While watching a course or reading a chapter:

* Draw diagrams.
* Write only the important ideas.
* Explain concepts in your own words.
* Don't try to create "perfect" notes.

Think of this as your working notebook.

---

### 2. Consolidate digitally

Later, spend 10–20 minutes creating a clean reference note.

For example:

```text
RHEL
 ├── Users
 ├── Permissions
 ├── SELinux
 ├── systemd
 ├── Storage
 ├── Networking
 └── Firewalld
```

Each topic becomes a page that you can expand over time.

---

### 3. Build a "cheat sheet"

Keep a separate collection of frequently used commands.

For example:

```bash
ip addr
ip route
ss -tulpn
journalctl -xe
systemctl status sshd
firewall-cmd --list-all
nmcli connection show
```

This becomes invaluable during labs and troubleshooting.

---

## If I had to choose only one

It depends on your goal.

* **To understand and remember:** handwriting has the edge.
* **To build a long-term reference:** typing is more practical.

If you're aiming to become a Linux or network engineer, I wouldn't rely exclusively on either.

---

## A workflow many professionals use

Many engineers settle into something like this:

* 📖 Learn from a book, video, or documentation.
* ✍️ Handwrite diagrams, packet flows, and key concepts.
* 💻 Convert the important material into organized digital notes.
* 🖥️ Practice in a lab (VMs, containers, or cloud instances).
* 📝 Update your notes with what you learned from real troubleshooting.

The last step is often the most valuable. Notes based on commands you actually ran, errors you encountered, and how you fixed them become far more useful than notes copied from a textbook.

For system administration and networking, the combination of **digital notes + hands-on labs + a small amount of handwritten sketching** tends to provide the best balance between learning, retention, and long-term usefulness.

For **IT, Linux, networking, and cybersecurity**, the trend today is actually moving **away from long document-style notes** (Google Docs, Word) and toward **Markdown-based knowledge bases**. The reason is simple: technical knowledge grows into hundreds of interconnected topics, and Markdown keeps things lightweight, portable, and easy to search.

Here's how I'd rank the common options.

| Tool                               | Best for                      | My rating |
| ---------------------------------- | ----------------------------- | --------- |
| Markdown (Obsidian, VS Code, etc.) | Long-term technical knowledge | ⭐⭐⭐⭐⭐     |
| OneNote                            | Mixed notes with drawings     | ⭐⭐⭐⭐☆     |
| Google Docs                        | Reports, collaboration        | ⭐⭐⭐☆☆     |
| Notion                             | Nice organization, databases  | ⭐⭐⭐⭐☆     |
| Plain folders + Markdown           | Maximum portability           | ⭐⭐⭐⭐⭐     |

---

## My recommendation

For someone studying:

* RHEL
* Linux
* Networking
* Docker
* Kubernetes
* Bash
* Security
* Cloud

I'd use **Markdown files** organized into folders.

Example:

```text
Knowledge Base/

├── Linux/
│   ├── Filesystem.md
│   ├── Permissions.md
│   ├── Users.md
│   ├── Systemd.md
│   └── SELinux.md
│
├── Networking/
│   ├── TCP.md
│   ├── UDP.md
│   ├── DNS.md
│   ├── VLAN.md
│   └── Routing.md
│
├── Bash/
│
├── Docker/
│
├── Kubernetes/
│
├── RHCSA/
│
└── Cheat Sheets/
```

No databases.

No complicated templates.

Just folders.

---

# A clean note format

One topic = one page.

For example:

````markdown
# TCP

## What is it?

Reliable transport protocol.

---

## Why?

Guarantees delivery.

---

## Diagram

Client
   |
 SYN
   |
Server

---

## Key Features

- Reliable
- Ordered
- Retransmission
- Flow Control

---

## Commands

```bash
ss -t
tcpdump
netstat
````

---

## Troubleshooting

Connection refused

↓

Service running?

↓

Firewall?

↓

Correct port?

---

## Remember

* Three-way handshake
* ACKs
* Sequence numbers

````

Notice something important:

There are almost no paragraphs.

---

# Use headings instead of walls of text

Instead of this

> TCP is a transport layer protocol that...

Do

```markdown
## TCP

Purpose

Reliable communication.

Used by

- HTTP
- HTTPS
- SSH

Strengths

- Ordered
- Reliable

Weaknesses

- Slower than UDP
````

Much easier to scan.

---

# Keep commands separate

Don't mix explanations and commands.

Example

````markdown
## Commands

Show IP

```bash
ip addr
````

Show routes

```bash
ip route
```

Show sockets

```bash
ss -tulpn
```

````

---

# Draw using ASCII

No need to paste pictures for everything.

```text
Internet

↓

Router

↓

Switch

↓

PC
````

or

```text
Client

↓

DNS

↓

IP

↓

TCP

↓

HTTP

↓

Response
```

---

# Keep notes short

A page should answer:

* What?
* Why?
* How?
* Commands?
* Example?
* Troubleshooting?

That's it.

If it becomes 8 pages...

Split it.

Example

Instead of

```
Networking.md
```

have

```
ARP.md
DNS.md
TCP.md
UDP.md
Routing.md
DHCP.md
```

---

# Have one "lab" section

This is where most students stop—but it's one of the most valuable parts.

```markdown
## Lab

Created two VMs.

Enabled SSH.

Forgot firewall rule.

Connection failed.

Fixed using:

firewall-cmd --add-service=ssh

Verified with:

ss -tulpn
```

These notes are gold because they come from your own experience.

---

# Link related topics

Markdown lets you keep topics connected without repeating information.

For example:

```
TCP

Related

→ IP

→ DNS

→ HTTP

→ Firewall
```

This helps you build a network of knowledge rather than isolated pages.

---

# Keep a separate cheat sheet

Don't clutter your concept notes with every command you've ever seen.

Example:

```
Cheat Sheets

Linux Commands.md

Networking Commands.md

Docker Commands.md

Git Commands.md
```

These become your quick reference during labs.

---

# My favorite principle

I follow something like the **5-20 rule**:

* **5 seconds:** I should understand what the page is about.
* **20 seconds:** I should find the command or answer I need.

If I have to read several dense paragraphs just to remember how to restart a service, the note is too verbose.

---

## If I were starting from scratch today

I would use:

* ✍️ A simple notebook for sketches, packet flows, boot sequences, and quick explanations while learning.
* 💻 A folder of Markdown files (edited in Obsidian or VS Code) as my permanent knowledge base.
* 🧪 A "Labs" folder where every experiment has its own note: what I tried, what failed, and how I fixed it.
* 📋 A small set of cheat sheets for commands I use repeatedly.

That combination stays clean, scales well from beginner to professional, and avoids turning your notes into a giant wall of text. Over time, it becomes a searchable personal wiki rather than a collection of lecture transcripts.

I like to start step by step only, not full info at once. 

So, I have a book:
Networking Fundamentals When you want to set up networking on a server, your server needs a unique address on the network. For this purpose, you use Internet Protocol (IP) addresses. Currently, two versions of IP addresses are relevant:
IPv4 addresses: These are based on 32-bit addresses and have four octets, separated by dots, such as 192.168.10.100.IPv6 addresses: These are based on 128-bit addresses and are written in eight groups of hexadecimal numbers that are 16 bits each and separated by colons. An IPv6 address may look like fe80:badb:abe01:45bc:34ad:1313:6723:8798. In this chapter, you learn how to work with IPv4 addresses. IPv6 addresses are described only briefly (but in enough detail for you to deal with this protocol on the exam) because IPv4 is still the protocol used by most administrators. IP Addresses Originally, IP addresses were assigned to computers and routers. Nowadays, many other devices also need IP addresses to communicate, such as smartphones, industrial equipment, and almost all other devices that are connected to the Internet. This chapter refers to all of those devices by using the word node. You’ll also occasionally encounter the word host. A host is typically a server providing services on the network. To make it easier for computers to communicate with one another, every IP address belongs to a specific network, and to communicate with computers on another network, a router is used. A router is a machine (often dedicated hardware that has been created for that purpose) that connects networks to one another. To communicate on the Internet, every computer needs a worldwide unique IP address. These addresses are scarce; a theoretical maximum of 4 billion IP addresses is available, and that is not enough to provide every device on the planet with an IP address. IPv6 is the ultimate solution for that problem because a very large number of IP addresses can be created in IPv6. Because many networks still work with IPv4, though, another solution exists: private network addresses.

And so on...

+ this
So, 192.168.1.45 where: - network part 24 bits - host part 8 bits 254 machines possible: where 2 addresses are missed? - one is for network address: 192.168.1.0 - one is for broadcast address: 192.168.1.255

Network mask - defines how amny bits reserved for network and how many for host. Le /24 signifie : les 24 premiers bits identifient le réseau.

Notation CIDR Signification Partie réseau Partie hôte /24 24 bits pour le réseau 3 octets (24 bits) 1 octet (8 bits) /16 16 bits pour le réseau 2 octets (16 bits) 2 octets (16 bits) /8 8 bits pour le réseau 1 octet (8 bits) 3 octets (24 bits)

OR

/24 255.255.255.0 Les 3 premiers octets sont fixes (255 = tous les bits à 1) /16 255.255.0.0 Les 2 premiers octets sont fixes /8 255.0.0.0 Le premier octet est fixe

/24 == 255.255.255.0

As example: 192.168.0.0/16 = one big address block

where: - 192.168 is the fixed network prefix - 0.0: host part, 65534 usable devices (2 reserved)

So, the piicture can be: Masque Adresses totales Machines utilisables Cas d'usage typique /32 1 1 Route host (IP unique), ACL, tunnels /31 2 2 Liaison point-à-point moderne (RFC 3021) /30 4 2 Liaison point-à-point classique /28 16 14 Petit réseau (équipe) /26 64 62 Sous-réseau département /24 256 254 Réseau local standard /16 65 536 65 534 Grande entreprise, VPC cloud /8 16 777 216 16 777 214 Très grand réseau Cas spéciaux /31 et /32

/31 : Normalisé par RFC 3021, ce masque permet 2 adresses utilisables (pas de broadcast ni d'adresse réseau "sacrifiée"). Très courant sur les liens point-à-point entre routeurs.

/32 : Une seule adresse. Utilisé pour les routes host (routage vers une IP précise), les règles de firewall, ou les interfaces de loopback.

1.2 Check with command if ip addresses in the same network Ex: ip route get 10.45.128.100 10.45.128.100 via 192.168.40.2 dev ens160 src 192.168.40.134 uid 1000 cache Where: via 192.168.40.2 means that it went through the route, which means it is a different network.

So, simple example: a. check you machine ip: ip addr show inet 192.168.40.134/24 brd 192.168.40.255 b. Obviously from the previous out the host is reserved for 0-255 for last bit, because mask is /24 c. If try: ip route get 192.168.41.1 where .41. it is already different network, because current network is resolved with mask 24 d. Thus, output is: 192.168.41.1 via 192.168.40.2 dev ens160 src 192.168.40.134 uid 1000 Means, got out via router, hence, another network. e. Now, try the same network: ip route get 192.168.40.20 Output: 192.168.40.20 dev ens160 src 192.168.40.134 uid 1000 As a result of the output, the ip was resoved in the same network, because it belongs to the same network.

1.3 How to calculate if the ip in the same network or not:

The whole purpose of subnet masks (/24, /21, /27, etc.) is to let a machine determine: “Is the destination on my local network, or do I need to send traffic to a router?” For humans, masks like /8, /16, /24 are easy because they align with full decimal blocks (octets).

Example: 192.168.40.x clearly looks like one network.

But masks like /21 or /27 split bits inside an octet, so the network boundary is no longer visually obvious in decimal notation.

That is why computers use binary AND operations: - to precisely determine the network portion of an IP - and compare whether two machines belong to the same subnet. So the “problem” is not the calculation itself. The real concern is: Humans see IPs in decimal blocks, but networks are actually defined at the bit level.

1.3.1 Caclulation rule

Rule is simple: (IP1 AND masque) == (IP2 AND masque)

Example 1. Same network - simple mask, /24
Input:

IP1: 192.168.40.5/24
IP2: 192.168.40.222/24
Mask: 255.255.255.0
IP1 :    192.168.40.5      → 11000000.10101000.00101000.00000101
Mask :   255.255.255.0     → 11111111.11111111.11111111.00000000
                               ────────────────────────────────────
Network: 192.168.40.0      → 11000000.10101000.00101000.00000000


IP2 :    192.168.40.222    → 11000000.10101000.00101000.11011110
Mask :   255.255.255.0     → 11111111.11111111.11111111.00000000
                               ────────────────────────────────────
Network: 192.168.40.0      → 11000000.10101000.00101000.00000000
Both calculations produce the same network address (192.168.40.0), so both machines are on the same subnet.

Example 2. Same network- more complex mask Input:

IP1: 192.168.40.5/21
IP2: 192.168.47.222/21
Mask: 255.255.248.0
IP1 :    192.168.40.5      → 11000000.10101000.00101000.00000101
Mask :   255.255.248.0     → 11111111.11111111.11111000.00000000
                               ────────────────────────────────────
Network: 192.168.40.0      → 11000000.10101000.00101000.00000000


IP2 :    192.168.47.222    → 11000000.10101000.00101111.11011110
Mask :   255.255.248.0     → 11111111.11111111.11111000.00000000
                               ────────────────────────────────────
Network: 192.168.40.0      → 11000000.10101000.00101000.00000000
Even though the third octet differs (40 vs 47), both calculations produce the same network address (192.168.40.0), so both machines are on the same subnet. 

So, my idea is to start from the fundamentals - to understand them. 

Then, to have the real, clear notes for those. Not just messy notes that I can hardly read. But clear hybrid approach notes. So, let's say we start from scratch I want to have the explanation what is the IP, why used, where, what is the 32, 128 bits, how to calculate bits, I want to know the real core and understand those properly. So, can we build the plan, I copy that and then we follow

