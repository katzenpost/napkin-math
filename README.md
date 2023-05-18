
# napkin math for katzenpost

Before we can do some useful napkin math we first need to collection
various statistics and benchmarks. This effort is currently underway.


## katzenpost napkin math

We are in fact the only project in the world using PQNoise
(our Noise protocol string is ``Noise_pqXX_Kyber768X25519_ChaChaPoly_BLAKE2s``).
How does this affect our performance?

Here's a benchmark that measures the client and server performing the handshake
and then the server encrypts a plaintext message and the client decrypts it:

```
BenchmarkPQNoise-8   	    1526	    758631 ns/op
```

The classical Noise XX (`Noise_XX_25519_ChaChaPoly_BLAKE2s`)
has this performance for the same test:

```
BenchmarkClassicalNoise-8   	    5160	    230530 ns/op
```

### The Sphinx cryptographic packet format benchmarks

NIKE Sphinx with X25519 is slower than KEM Sphinx with X25519 AND
Kyber because NIKE Sphinx uses two public key operations per hop.


NIKE-Sphinx with X25519:
---

```
BenchmarkX25519SphinxUnwrap-8              	    7656	    144711 ns/op

18088 ns/op
```

KEM-Sphinx with Kyber:
---

```
BenchmarkKEMSphinxUnwrapKyber512-8                33474         40507 ns/op
BenchmarkKEMSphinxUnwrapKyber768-8                21907         54911 ns/op
BenchmarkKEMSphinxUnwrapKyber1024-8               18546         67044 ns/op
```


KEM-Sphinx with hybrid KEM Kyber and X25519:
---

```
BenchmarkKEMSphinxUnwrapKyber768X25519-8          15866         76339 ns/op
```

NIKE-Sphinx with CTIDH:
---

```
BenchmarkCtidh512SphinxUnwrap-8                       3     336995975 ns/op
BenchmarkCtidh1024SphinxUnwrap-8                      1    3316483993 ns/op
BenchmarkCtidh2048SphinxUnwrap-8                      1    17056742100 ns/op
```
