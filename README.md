
# napkin math

---

# latency

## network latency

| average latency between mix nodes |
| :---:                             |
| 78 milliseconds                   |

Let's assume a 78 millisecond average latency between mix nodes of the mix network.


## poisson mix latency

Sampling from poisson distributions happens for these various symbols mentioned
in the Loopix paper:

| Symbol | Description |
| :---   | ---:        |
| LambdaP / λP | client payload traffic rate |
| LambdaD / λD | client drop decoy traffic rate |
| LambdaL / λL | client loop decoy traffic rate |
| Mu / μ  | the mean delay at each mix |
| LambdaM | mix node loop decoy traffic rate |


However for our calculation of latecy, all we care about is Mu:

| Mu | average latency per hop | avgerage latency per 5 hops | rtt (10 hops) |
| :---   | :---:                   | :---: | ---:                                |
| 0.001 | 1s | 5s | 10s |
| 0.002 | 487ms | 2.4s | 4.8s |
| 0.0005 | 1.925s | 9.5s | 19s |


## noise latency

| Noise protocol | nanoseconds/op | seconds/op |
| :---           |  ---:          | ---:       |
| Noise_XX_25519_ChaChaPoly_BLAKE2s | 271939 | 0.000271939 |
| Noise_pqXX_Kyber768X25519_ChaChaPoly_BLAKE2s | 843680 | 0.00084368 |


* This test only performs cryptographic operations. Our noise
benchmark test measures the client and server performing the handshake
and then the server encrypts a plaintext message and the client
decrypts it:

See our specification for more information:
* Noise base wire protocol https://github.com/katzenpost/katzenpost/blob/main/docs/specs/wire-protocol.rst


## sphinx latency

| Primitive | Sphinx type | nanoseconds/op | seconds/op |
| :---      |  :---:      |     ---:       | ---:       |
| X25519 | KEM | 80093 | 8.009×10−5 |
| X25519 | NIKE | 160233 | 0.000160233 |
| Kyber512 | KEM | 43758 | 4.3758e-5 |
| Kyber768 | KEM | 57049 | 5.7049e-5 |
| Kyber1024 | KEM | 72173 | 7.2173e-5 |
| **Kyber768 X25519 Hybrid** | KEM | **87816** | 8.7816e-5 |
| CTIDH512 | NIKE | 336995975 | 0.336995975 |
| CTIDH1024 | NIKE | 18599579037 | 18.599579037 |
| CTIDH2048 | NIKE | 17056742100 | 17.0567421 |
| CTIDH512 | KEM |  | |
| CTIDH1024 | KEM | 11408217346 | 11.408217346 |
| CTIDH2048 | KEM |  | |

* NIKE Sphinx is slower than KEM Sphinx because NIKE Sphinx must perform
two public key operations per hop.

* However the other tradeoff is that KEM
Sphinx requires a much bigger cryptographic object size: the sphinx packet
header size will be very large!

See our specifications for more information:

* NIKE Sphinx https://github.com/katzenpost/katzenpost/blob/main/docs/specs/sphinx.rst
* KEM Sphinx https://github.com/katzenpost/katzenpost/blob/main/docs/specs/kemsphinx.rst

<BR><BR>

# bandwidth overhead

## Sphinx overhead

| Primitive | Sphinx Type | Header Size | Num Hops |
| :---    |    :----:   |     ---:    | ---: |
| X25519  |  NIKE       |   476     | 5 |
| X25519  |  NIKE       |   886     | 10 |
| Kyber512|  KEM        |    5052   | 5 | 
| Kyber512|  KEM        |    9302   | 10 | 
| Kyber768|  KEM        |    6972      | 5|
| Kyber768|  KEM        |    12822      | 10|
| Kyber1024| KEM        |    9852         | 5|
| Kyber1024| KEM        |   18102          |10 |
| **Kyber768 X25519 Hybrid** | KEM |   **7164**   | 5|
| Kyber768 X25519 Hybrid | KEM |   13174   |10 |

Since we can set the Sphinx packet payload to any size we want,
we have a lot of control over it's overhead ratio.

## Noise overhead

* Our Noise transport messages incure a 16 byte overhead byte we use ChaCha20-Poly1305.

* Currently we have set the maximum Noise message size to 1300000 bytes.

## Additional protocol overheads

| Name | Overhead |
| :--- | :----:   |
| IPv4 | 20 bytes |
| UDP | 8 bytes |
| QUIC v1 | 20 bytes |
| appox. TLS | 29 bytes |
| TOTAL: | 77 |

1474 (IP Packet) - 20 (IPv4) - 8 (UDP) - 20 (QUIC) - 29 (TLS) = 1397 bytes


## Sphinx + QUIC overheads formula

We calculate the overhead ratio for
transmitting application payloads over Sphinx/QUIC/UDP/IPv4.

1. `p` is the Sphinx payload size
2. `h` is the Sphinx header size
3. `s` is the Sphinx packet size, which is `p + h`
4. `q` is the overhead impose by QUIC/UDP/IPv4 (which is 77 bytes)
5. `i` is the payload size that can be transported in single QUIC packet (1397 bytes)

The Sphinx packet will be divided into many QUIC packets. Let N equal the number of QUIC
packets needed to transport a Sphinx packet:

`N = ceil(s / i)`

The total overhead for transmitting the Sphinx packet over IP/UDP/QUIC would then be:

`N * q`

Therefore, the bandwidth overhead ratio can be calculated as the total overhead divided by the total data transmitted. This will give us:

```
Bandwidth Overhead Ratio = (N * q) / (s + (N * q))
```
