
# napkin math

---



### network latency

| average latency between mix nodes |
| :---:                             |
| 78 milliseconds                   |

Let's assume a 78 millisecond average latency between mix nodes of the mix network.


### poisson mix latency

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


### noise latency

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


### sphinx latency

| Primitive | Sphinx type | nanoseconds/op | seconds/op |
| :---      |  :---:      |     ---:       | ---:       |
| X25519 | KEM | 80093 | 8.009×10−5 |
| X25519 | NIKE | 160233 | 0.000160233 |
| Kyber512 | KEM | 43758 | 4.3758e-5 |
| Kyber768 | KEM | 57049 | 5.7049e-5 |
| Kyber1024 | KEM | 72173 | 7.2173e-5 |
| Kyber768 X25519 Hybrid | KEM | 87816 | 8.7816e-5 |
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
