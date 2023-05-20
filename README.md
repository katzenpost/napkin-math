
# napkin math

Before we can do an Enrico Fermi style estimation in the style of
"How many piano tuners are in Chicago?"  we first have to collect some
statistics. This effort is currently underway.


## noise

| Noise protocol | nanoseconds/op |
| :---           |  ---:          |
| Noise_XX_25519_ChaChaPoly_BLAKE2s | 28816 |
| Noise_pqXX_Kyber768X25519_ChaChaPoly_BLAKE2s | 94828 |


* This test only performs cryptographic operations. Our noise
benchmark test measures the client and server performing the handshake
and then the server encrypts a plaintext message and the client
decrypts it:

See our specification for more information:
* Noise base wire protocol https://github.com/katzenpost/katzenpost/blob/main/docs/specs/wire-protocol.rst


### sphinx

| Primitive | Sphinx type | nanoseconds/op |
| :---      |  :---:      |     ---:       |
| X25519 | NIKE | 18088 |
| Kyber512 | KEM | 5063 |
| Kyber768 | KEM | 6863 |
| Kyber1024 | KEM | 8380 |
| Kyber768 X25519 Hybrid | KEM | 9542 |
| CTIDH512 | NIKE | 42124496 |
| CTIDH1024 | NIKE | 414560499 |
| CTIDH2048 | NIKE | 2132092762 |
| CTIDH512 | KEM |  |
| CTIDH1024 | KEM |  |
| CTIDH2048 | KEM |  |

* NIKE Sphinx is slower than KEM Sphinx because NIKE Sphinx must perform
two public key operations per hop.

* However the other tradeoff is that KEM
Sphinx requires a much bigger cryptographic object size: the sphinx packet
header size will be very large!

See our specifications for more information:

* NIKE Sphinx https://github.com/katzenpost/katzenpost/blob/main/docs/specs/sphinx.rst
* KEM Sphinx https://github.com/katzenpost/katzenpost/blob/main/docs/specs/kemsphinx.rst
