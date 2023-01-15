iptgen
===========

iptgen is a tool to generate network packets from scripts to play them onto your netowrk or create a pcap file.


Installing
----------

Install in the standard way:

### Windows

1. Install `npcap` driver. Visit https://nmap.org/npcap/ to download and install it.

2. Extract `iptgen.win32.zip`.


### Linux

1. Choose the archive file appropriate for your platform, `iptgen.linux-x86_64.tar.gz` or `iptgen.linux-i686.tar.gz`, and extract it.



Binaries
----------
Here are binaries:
* [Linux 64-bit](https://github.com/spearmin10/iptgen/releases/download/0.6.0/iptgen.linux-x86_64.tar.gz)
* [Linux 32-bit](https://github.com/spearmin10/iptgen/releases/download/0.6.0/iptgen.linux-i686.tar.gz)
* [Windows 32-bit](https://github.com/spearmin10/iptgen/releases/download/0.6.0/iptgen.win32.zip)


Usage
----------

For playing packets onto your network.

```
iptgen.bin --in.file <script-file> --out.eth <ifname>
```

e.g.
```
** Linux
sudo iptgen.bin --in.file ./scripts/http-upload.json --out.eth eth0

** Windows
iptgen.exe --in.file ./scripts/http-upload.json --out.eth Ethernet0
```


For creating a pcap file.

```
iptgen.bin --in.file <script-file> --out.file <filename>
```

e.g.
```
** Linux
sudo iptgen.bin --in.file ./scripts/http-upload.json --out.file http.pcap

** Windows
iptgen.exe --in.file ./scripts/http-upload.json --out.file http.pcap
```


To see all options that are available, run:

```
iptgen.bin --help
```


Syntex for scripts
----------

A script is text that a list of `Process` or `String (comment)` are concatenated.

### Process

> **Data Type**: Object[String, Any] or Array[Object[String, Any]]

<details><summary>Details</summary>

| **Key** | **Type** | **Description** |
| --- | --- | --- |
| client | String | Client IP Address (e.g. 192.168.1.2), Port numer is optional. |
| server | String | Server IP Address (e.g. 1.2.3.4:80), Port numer is optional. |
| eth.src (Optional) | String | Client MAC Address (e.g. 11:22:33:44:55:66) |
| eth.dst (Optional) | String | Server MAC Address (e.g. 11:22:33:44:55:66) |
| sequence | Sequence | Sequence of sessions |

</details>

### Sequence

> **Data Type**: Array[Operation]


### Operation

#### Standard Syntex

> **Data Type**: Object[String, Any]

#### Compact Syntex

> **Data Type**: Operation or Array[Operation]



### Operation: none
<details><summary>
No operation.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `none` |

```
{
  "op": "none"
}
```


#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `none` |


```
["none"]
```

</details>




### Operation: for
<details><summary>
`for-loop` statement for specifying iteration.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `for` |
| l.begin | Number | Start value of the counter |
| l.end | Number | End value of the counter |
| l.step | Number | Specifies the amount the counter is increased (default:1) |
| l.name | String | Name of the counter |
| l.sequence | Sequence | Sequence of operations in the loop |


```
{
  "op": "for",
  "l.begin": 0,
  "l.end": 10,
  "l.name": "i",
  "l.sequence": [
    ["dns.q.a", "www.domain.com", "3.3.3.3"]
  ]
}
```


#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `for` |
| [1] | Array[l.begin, l.end, l.strp] | [0]: l.begin, [1]: l.end, [2]: l.step (default:1) |
| [2] | String | l.name |
| [3] | Sequence | l.sequence |

```
["for", [0, 10], "i",
  [
    ["dns.q.a", "www.domain.com", "2.2.2.2"]
  ]
]
```

</details>




### Operation: loop
<details><summary>
Run operations in the infinite loop
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `loop` |
| l.sequence | Sequence | Sequence of operations in the loop |

```
{
  "op": "loop",
  "l.sequence": [
    ["dns.q.a", "www.domain.com", "3.3.3.3"]
  ]
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `loop` |
| [1] | Sequence | l.sequence |

```
["loop",
  [
    ["dns.q.a", "www.domain.com", "2.2.2.2"]
  ]
]
```

</details>




### Operation: for.session
<details><summary>
`for-loop` statement for specifying iteration.
The session is closed whenever looping back or when breaking the loop.
A new port is assigned for a new session when closing the session.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `for.session` |
| l.begin | Number | Start value of the counter |
| l.end | Number | End value of the counter |
| l.step | Number | Specifies the amount the counter is increased |
| l.name | String | Name of the counter |
| l.sequence | Sequence | Sequence of operations in the loop |

```
{
  "op": "for.session",
  "l.begin": 0,
  "l.end": 10,
  "l.name": "i",
  "l.sequence": [
    ["tcp.send", "text", "Hello"],
    ["tcp.recv", "text", "Hello"]
  ]
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `for.session` |
| [1] | Array[l.begin, l.end, l.strp] | [0]: l.begin, [1]: l.end, [2]: l.step |
| [2] | String | l.name |
| [3] | Sequence | l.sequence |

```
["for.session", [0, 10], "i",
  [
    ["tcp.send", "text", "Hello"],
    ["tcp.recv", "text", "Hello"]
  ]
]
```

</details>




### Operation: loop.session
<details><summary>
Run operations in the infinite loop. The session is closed whenever looping back.
A new port is assigned for a new session when closing the session.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `loop.session` |
| l.sequence | Sequence | Sequence of operations in the loop |

```
{
  "op": "loop.session",
  "l.sequence": [
    ["tcp.send", "text", "Hello"],
    ["tcp.recv", "text", "Hello"]
  ]
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `loop.session` |
| [1] | Sequence | l.sequence |

```
["loop.session", [0, 10], "i",
  [
    ["tcp.send", "text", "Hello"],
    ["tcp.recv", "text", "Hello"]
  ]
]
```

</details>



### Operation: for.process
<details><summary>
`for-loop` statement for specifying iteration.
A new process separated from the session is created on each iteration.
New client/server IPs can be used in the processes.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `for.process` |
| l.begin | Number | Start value of the counter |
| l.end | Number | End value of the counter |
| l.step | Number | Specifies the amount the counter is increased (default:1) |
| l.name | String | Name of the counter |
| l.sequence | Process | Sequence of processes in the loop |

```
{
  "op": "for.process",
  "l.begin": 0,
  "l.end": 10,
  "l.name": "i",
  "l.sequence": {
    "client": "1.1.1.1", 
    "server": "2.2.2.2:80",
    "sequence": [
      ["tcp.send", "text", "Hello"],
      ["tcp.recv", "text", "Hello"]
    ]
  }
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `for.process` |
| [1] | Array[l.begin, l.end, l.strp] | [0]: l.begin, [1]: l.end, [2]: l.step (default:1) |
| [2] | String | l.name |
| [3] | Process | l.sequence |

```
["for.process", [0, 10], "i",
  {
    "client": "1.1.1.1", 
    "server": "2.2.2.2:80",
    "sequence": [
      ["tcp.send", "text", "Hello"],
      ["tcp.recv", "text", "Hello"]
    ]
  }
]
```

</details>




### Operation: comment
<details><summary>
Comment statement. The statement doesn't affect the control flow.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `comment` or `` (empty) |
| <any> | Any | Comment |

```
{
  "op": "comment",
  "": "This is a comment."
}
```

```
{
  "op": "",
  "": "This is a comment."
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `comment` or `` (empty) |
| [1-] | Any | Comment |

```
["comment", "This is a comment."]
```

```
["", "This is a comment."]
```


</details>




### Operation: process
<details><summary>
A new process separated from the session is created.
New client/server IPs can be used in the process.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `process` |
| sequence | Process | Sequence of processes |


```
{
  "op": "process",
  "sequence": [
    {
      "client": "1.1.1.1",
      "server": "2.2.2.2:9999",
      "sequence": [
        ["tcp.send", "text", "Hello"]
      ]
    },
    {
      "client": "3.3.3.3",
      "server": "4.4.4.4:9999",
      "sequence": [
        ["tcp.send", "text", "Hello"]
      ]
    }
  ]
}
```


#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `process` |
| [1] | Process | sequence |

```
["process",
  [
    {
      "client": "1.1.1.1",
      "server": "2.2.2.2:9999",
      "sequence": [
        ["tcp.send", "text", "Hello"]
      ]
    },
    {
      "client": "3.3.3.3",
      "server": "4.4.4.4:9999",
      "sequence": [
        ["tcp.send", "text", "Hello"]
      ]
    }
  ]
]
```

</details>




### Operation: session.new
<details><summary>
The session is closed and a new port is assigned for a new session.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `session.new` |


```
{
  "op": "session.new"
}
```


#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `session.new` |

```
["session.new"]
```

</details>




### Operation: udp.send
<details><summary>
Generate UDP packets to be generated when sending the payload to the server.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `udp.send` |
| p.type | String | Payload type (see Payload) |
| p.value | Any | Payload data (see Payload) |

```
{
  "op": "udp.send",
  "p.type": "text",
  "p.value": "Hello"
}
```


#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `udp.send` |
| [1] | String | p.type |
| [2] | Any | p.value |

```
["udp.send", "text", "Hello"]
```

</details>




### Operation: udp.recv
<details><summary>
Generate UDP packets to be received when receiving the payload from the server.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `udp.recv` |
| p.type | String | Payload type (see Payload) |
| p.value | Any | Payload data (see Payload) |

```
{
  "op": "udp.recv",
  "p.type": "text",
  "p.value": "Hello"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `udp.recv` |
| [1] | String | p.type |
| [2] | Any | p.value |

```
["udp.recv", "text", "Hello"]
```

</details>




### Operation: tcp.syn.stateless
<details><summary>
Generate a TCP SYN packet to the server.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.syn.stateless` |

```
{
  "op": "tcp.syn.stateless"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.syn.stateless` |

```
["tcp.syn.stateless"]
```

</details>




### Operation: tcp.syn+synack.stateless
<details><summary>
Generate a TCP SYN packet to the server and an ACK from the server.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.syn+synack.stateless` |

```
{
  "op": "tcp.syn+synack.stateless"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.syn+synack.stateless` |

```
["tcp.syn+synack.stateless"]
```

</details>



### Operation: tcp.handshake
<details><summary>
Generate packets of TCP three-way handshaking.
No packets are generated when the session has already been established.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.handshake` |

```
{
  "op": "tcp.handshake"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.handshake` |

```
["tcp.handshake"]
```

</details>



### Operation: tcp.handshake.stateless
<details><summary>
Generate packets of TCP three-way handshaking no matter the session state.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.handshake.stateless` |

```
{
  "op": "tcp.handshake.stateless"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.handshake.stateless` |

```
["tcp.handshake.stateless"]
```

</details>



### Operation: tcp.send
<details><summary>
Generate packets sent and received when sending the payload in TCP.
If the TCP session has not established yet, it is done prior to sedning it.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.send` |
| p.type | String | Payload type (see Payload) |
| p.value | Any | Payload data (see Payload) |

```
{
  "op": "tcp.send",
  "p.type": "text",
  "p.value": "Hello"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.send` |
| [1] | String | p.type |
| [2] | Any | p.value |

```
["tcp.send", "text", "Hello"]
```

</details>



### Operation: tcp.send.stateless
<details><summary>
Generate packets sent and received when sending the payload in TCP.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.send.stateless` |
| p.type | String | Payload type (see Payload) |
| p.value | Any | Payload data (see Payload) |

```
{
  "op": "tcp.send.stateless",
  "p.type": "text",
  "p.value": "Hello"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.send.stateless` |
| [1] | String | p.type |
| [2] | Any | p.value |

```
["tcp.send.stateless", "text", "Hello"]
```

</details>



### Operation: tcp.recv
<details><summary>
Generate packets sent and received when receiving the payload in TCP.
If the TCP session has not established yet, it is done prior to receiving it.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.recv` |
| p.type | String | Payload type (see Payload) |
| p.value | Any | Payload data (see Payload) |

```
{
  "op": "tcp.recv",
  "p.type": "text",
  "p.value": "Hello"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.recv` |
| [1] | String | p.type |
| [2] | Any | p.value |

```
["tcp.recv", "text", "Hello"]
```

</details>



### Operation: tcp.recv.stateless
<details><summary>
Generate packets sent and received when receiving the payload in TCP.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.recv.stateless` |
| p.type | String | Payload type (see Payload) |
| p.value | Any | Payload data (see Payload) |

```
{
  "op": "tcp.recv.stateless",
  "p.type": "text",
  "p.value": "Hello"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.recv.stateless` |
| [1] | String | p.type |
| [2] | Any | p.value |

```
["tcp.recv.stateless", "text", "Hello"]
```

</details>



### Operation: tcp.shutdown
<details><summary>
Generate packets of a TCP shutdown handshaking initiated by the client.
No packets are generated when the session is not active.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.shutdown` |

```
{
  "op": "tcp.shutdown"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.shutdown` |

```
["tcp.shutdown"]
```

</details>



### Operation: tcp.shutdown.stateless
<details><summary>
Generate packets of a TCP shutdown handshaking initiated by the client.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.shutdown.stateless` |

```
{
  "op": "tcp.shutdown.stateless"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.shutdown.stateless` |

```
["tcp.shutdown.stateless"]
```

</details>



### Operation: tcp.shutdown-by-peer
<details><summary>
Generate packets of a TCP shutdown handshaking initiated by the server.
No packets are generated when the session is not active.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.shutdown-by-peer` |

```
{
  "op": "tcp.shutdown-by-peer"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.shutdown-by-peer` |

```
["tcp.shutdown-by-peer"]
```

</details>



### Operation: tcp.shutdown.shutdown-by-peer.stateless
<details><summary>
Generate packets of a TCP shutdown handshaking initiated by the server.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.shutdown.shutdown-by-peer.stateless` |

```
{
  "op": "tcp.shutdown.shutdown-by-peer.stateless"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.shutdown.shutdown-by-peer.stateless` |

```
["tcp.shutdown.shutdown-by-peer.stateless"]
```

</details>



### Operation: tcp.clear.state
<details><summary>
Reset the internal state of the TCP session.
Sending or receiving data in TCP will restart TCP handshaking prior to doing it.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `tcp.clear.state` |

```
{
  "op": "tcp.clear.state"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `tcp.clear.state` |

```
["tcp.clear.state"]
```

</details>



### Operation: ssl.handshake
<details><summary>
Generate packets of SSL handshaking.
No packets are generated when it is already established.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `ssl.handshake` |

```
{
  "op": "ssl.handshake"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `ssl.handshake` |

```
["ssl.handshake"]
```

</details>



### Operation: ssl.send
<details><summary>
Generate packets sent and received in sending the payload in SSL.
If a SSL session has not established yet, it will be done with SSL handshaking prior to doing it.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `ssl.send` |
| p.type | String | Payload type (see Payload) |
| p.value | Any | Payload data (see Payload) |

```
{
  "op": "ssl.send",
  "p.type": "text",
  "p.value": "Hello"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `ssl.send` |
| [1] | String | p.type |
| [2] | Any | p.value |

```
["ssl.send", "text", "Hello"]
```

</details>



### Operation: ssl.recv
<details><summary>
Generate packets sent and received in receiving the payload in SSL.
If a SSL session has not established yet, it will be done with SSL handshaking prior to doing it.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `ssl.recv` |
| p.type | String | Payload type (see Payload) |
| p.value | Any | Payload data (see Payload) |

```
{
  "op": "ssl.recv",
  "p.type": "text",
  "p.value": "Hello"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `ssl.recv` |
| [1] | String | p.type |
| [2] | Any | p.value |

```
["ssl.recv", "text", "Hello"]
```

</details>



### Operation: ssl.shutdown
<details><summary>
Generate packets sent and received in SSL shutdown sequence initiated by the client.
No packets are generated when the SSL session is not ready.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `ssl.shutdown` |

```
{
  "op": "ssl.shutdown"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `ssl.shutdown` |

```
["ssl.shutdown"]
```

</details>



### Operation: ssl.shutdown-by-peer
<details><summary>
Generate packets sent and received in SSL shutdown sequence initiated by the server.
No packets are generated when the SSL session is not ready.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value** |
| --- | --- | --- |
| op | String | `ssl.shutdown-by-peer` |

```
{
  "op": "ssl.shutdown-by-peer"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `ssl.shutdown-by-peer` |

```
["ssl.shutdown-by-peer"]
```

</details>



### Operation: dns.q.a
<details><summary>
Generate packets sent and received in a DNS A record query transaction.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value / Description** |
| --- | --- | --- |
| op | String | `dns.q.a` |
| q.name | String | DNS query name |
| r.answers (Optional) | String or Array[String] or Array[Object[String,String] | Resolved IPv4 addresses |

```
{
  "op": "dns.q.a",
  "q.name": "www.domain.com"
}
```

```
{
  "op": "dns.q.a",
  "q.name": "www.domain.com",
  "r.answers": {
    "a": "1.1.1.1"
  }
}
```

```
{
  "op": "dns.q.a",
  "q.name": "www.domain.com",
  "r.answers": "1.1.1.1"
}
```

```
{
  "op": "dns.q.a",
  "q.name": "www.domain.com",
  "r.answers": ["1.1.1.1", "2.2.2.2"]
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `dns.q.a` |
| [1] | String | q.name |
| [1] (Optional) | String or Array[String] or Array[Object[String,String] | r.answers |

```
["dns.q.a", "www.domain.com"]
```

```
["dns.q.a", "www.domain.com", "1.1.1.1"]
```

```
["dns.q.a", "www.domain.com", ["1.1.1.1", "2.2.2.2"]]
```

</details>



### Operation: dns.q.aaaa
<details><summary>
Generate packets sent and received in a DNS AAAA record query transaction.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value / Description** |
| --- | --- | --- |
| op | String | `dns.q.aaaa` |
| q.name | String | DNS query name |
| r.answers (Optional) | String or Array[String] or Array[Object[String,String] | Resolved IPv6 addresses |

```
{
  "op": "dns.q.aaaa",
  "q.name": "www.domain.com"
}
```

```
{
  "op": "dns.q.aaaa",
  "q.name": "www.domain.com",
  "r.answers": {
    "aaaa": "2001:db8:a0b:12f0::1"
  }
}
```

```
{
  "op": "dns.q.aaaa",
  "q.name": "www.domain.com",
  "r.answers": "2001:db8:a0b:12f0::1"
}
```

```
{
  "op": "dns.q.aaaa",
  "q.name": "www.domain.com",
  "r.answers": ["2001:db8:a0b:12f0::1", "2001:db8:a0b:12f0::2"]
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `dns.q.aaaa` |
| [1] | String | q.name |
| [1] (Optional) | String or Array[String] or Array[Object[String,String] | r.answers |

```
["dns.q.aaaa", "www.domain.com"]
```

```
["dns.q.aaaa", "www.domain.com", "2001:db8:a0b:12f0::1"]
```

```
["dns.q.aaaa", "www.domain.com", ["2001:db8:a0b:12f0::1", "2001:db8:a0b:12f0::2"]]
```

</details>



### Operation: dns.q.txt
<details><summary>
Generate packets sent and received in a DNS TXT record query transaction.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value / Description** |
| --- | --- | --- |
| op | String | `dns.q.txt` |
| q.name | String | DNS query name |
| r.answers (Optional) | String or Array[String] or Array[Object[String,String] | Response texts |

```
{
  "op": "dns.q.txt",
  "q.name": "www.domain.com"
}
```

```
{
  "op": "dns.q.txt",
  "q.name": "www.domain.com",
  "r.answers": {
    "txt": "response-text"
  }
}
```

```
{
  "op": "dns.q.txt",
  "q.name": "www.domain.com",
  "r.answers": "response-text"
}
```

```
{
  "op": "dns.q.txt",
  "q.name": "www.domain.com",
  "r.answers": ["response-text1", "response-text2"]
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `dns.q.txt` |
| [1] | String | q.name |
| [1] (Optional) | String or Array[String] or Array[Object[String,String] | r.answers |

```
["dns.q.txt", "www.domain.com"]
```

```
["dns.q.txt", "www.domain.com", "response-text"]
```

```
["dns.q.txt", "www.domain.com", ["response-text1", "response-text2"]]
```

</details>



### Operation: sys.time.sleep
<details><summary>
Suspend the execution of the current session until the time-out interval elapses.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value / Description** |
| --- | --- | --- |
| op | String | `sys.time.sleep` |
| time | Number | The time interval in seconds. |

```
{
  "op": "sys.time.sleep",
  "time": 10
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `sys.time.sleep` |
| [1] | String | time |

```
["sys.time.sleep", 10]
```

</details>



### Operation: sys.time.drift
<details><summary>
Suspend the execution of the current session until the time-out interval elapses when playing in live.
The time-out interval given is skipped only when writing pcap files.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value / Description** |
| --- | --- | --- |
| op | String | `sys.time.drift` |
| time | Number | The time interval in seconds. |

```
{
  "op": "sys.time.drift",
  "time": 10
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `sys.time.drift` |
| [1] | String | time |

```
["sys.time.drift", 10]
```

</details>



### Operation: sys.print
<details><summary>
Print a message to stdout or stderr.
</summary><p/>

#### Standard Syntex

| **Key** | **Type** | **Value / Description** |
| --- | --- | --- |
| op | String | `sys.print` |
| text | String | A message to print |
| device (Optional) | String | `stdout` or `stderr` (default: stdout) |

```
{
  "op": "sys.print",
  "text": "Hello"
}
```

#### Compact Syntex

| **Index** | **Type** | **Value / Description** |
| --- | --- | --- |
| [0] | String | `sys.print` |
| [1] | String | text |
| [2] (Optional) | String | device |

```
["sys.print", "Hello"]
```

</details>



### Payload
<details><summary>
Payload data to send or receive.
</summary><p/>

#### Type

| **Name** | **Description** |
| --- | --- |
| text | Text. ${}-wrapped variable in the text is replaced with the value. |
| text.raw | Raw text. |
| urlenc | Use the decoded value of the text in URL encoding. ${}-wrapped variable in the payload is replaced with the value. |
| urlenc.raw | Use the decoded value of the text in URL encoding. |
| base64 | Use the decoded value of the text in base64. ${}-wrapped variable in the payload is replaced with the value. |
| base64.raw | Use the decoded value of the text in base64. |
| hex | Use the decoded value of the text in hex. ${}-wrapped variable in the payload is replaced with the value. |
| hex.raw | Use the decoded value of the text in hex. |
| file | Use the content of the file. ${}-wrapped variable in the content is replaced with the value. |
| file.raw | Use the content of the file. |
| exec | Use the data from stdout of the process executed. ${}-wrapped variable in the data is replaced with the value. |
| exec.raw | Use the data from stdout of the process executed. |
| multi | Concatenate payloads in different payload types. |
| comment | Comments. |
| (empty string) | Comments. |


#### Value
---
##### text / text.raw

* String

Text given in the value.

In:
```
"text message"
```

Out:
```
text message
```


* Array[String]

Concatenate each element with CRLF.

In:
```
[
  "line1",
  "line2",
  "line3"
]
```

Out:
```
line1[CR][LF]
line2[CR][LF]
line3[CR][LF]
```

* Array[String] in Array

Concatenate each element.

In:
```
[
  "line1",
  [
    "text1",
    "text2",
    "text3"
  ],
  "line3"
]
```


Out:
```
line1[CR][LF]
text1text2text3line3[CR][LF]
```

---
##### urlenc / urlenc.raw

* String

Decode the text given in URL encoding.

In:
```
"%3A%2F%2A"
```

Out:
```
:/*
```


* Array[String]

Concatenate each element and decode it in URL encoding.

In:
```
[
  "%3A",
  "%2F",
  "%2A"
]
```

Out:
```
:/*
```

---
##### base64 / base64.raw

* String

Decode the text given in base64.

In:
```
"YWJjZGU="
```

Out:
```
abcde
```


* Array[String]

Concatenate each element and decode it in base64.

In:
```
[
  "YWJj",
  "ZGU="
]
```

Out:
```
abcde
```

---
##### hex / hex.raw

* String

Decode the text given in hex.

In:
```
"6162636465"
```

Out:
```
abcde
```


* Array[String]

Concatenate each element and decode it in hex.

In:
```
[
  "61626",
  "36465"
]
```

Out:
```
abcde
```

---
##### file / file.raw

* String

Read the file of the name given.

In:
```
"file.dat"
```

Out:
```
<The content of file.dat>
```


* Array[String]

Concatenate each file.

In:
```
[
  "file1.dat",
  "file2.dat"
]
```

Out:
```
<The content of file1.dat><The content of file2.dat>
```



</details>

