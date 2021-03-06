# RSA

RSA (Rivest–Shamir–Adleman) is a public-key cryptosystem that is widely used for secure data transmission. It is also one of the oldest. The acronym "RSA" comes from the surnames of Ron Rivest, Adi Shamir and Leonard Adleman, who publicly described the algorithm in 1977. An equivalent system was developed secretly in 1973 at GCHQ (the British signals intelligence agency) by the English mathematician Clifford Cocks. That system was declassified in 1997.

In a public-key cryptosystem, the encryption key is public and distinct from the decryption key, which is kept secret (private). An RSA user creates and publishes a public key based on two large prime numbers, along with an auxiliary value. The prime numbers are kept secret. Messages can be encrypted by anyone, via the public key, but can only be decoded by someone who knows the prime numbers.

The security of RSA relies on the practical difficulty of factoring the product of two large prime numbers, the "factoring problem". Breaking RSA encryption is known as the RSA problem. Whether it is as difficult as the factoring problem is an open question. There are no published methods to defeat the system if a large enough key is used.

RSA is a relatively slow algorithm. Because of this, it is not commonly used to directly encrypt user data. More often, RSA is used to transmit shared keys for symmetric-key cryptography, which are then used for bulk encryption–decryption.

## Proofs of correctness

### Proof using Fermat's little theorem

![equation](equ.PNG)

Notes:

- We cannot trivially break RSA by applying the theorem (mod pq) because pq is not prime.
- In particular, the statement above holds for any e and d that satisfy ed ≡ 1 (mod (p − 1)(q − 1)), since (p − 1)(q − 1) is divisible by λ(pq), and thus trivially also by p − 1 and q − 1. However, in modern implementations of RSA, it is common to use a reduced private exponent d that only satisfies the weaker, but sufficient condition ed ≡ 1 (mod λ(pq)).
- This is part of the Chinese remainder theorem, although it is not the significant part of that theorem.

### Proof using Euler's theorem
Although the original paper of Rivest, Shamir, and Adleman used Fermat's little theorem to explain why RSA works, it is common to find proofs that rely instead on [Euler's theorem](https://en.wikipedia.org/wiki/Euler%27s_theorem).

![sec](Capture.PNG)

When m is not relatively prime to n, the argument just given is invalid. This is highly improbable (only a proportion of 1/p + 1/q − 1/(pq) numbers have this property), but even in this case, the desired congruence is still true. Either m ≡ 0 (mod p) or m ≡ 0 (mod q), and these cases can be treated using the previous proof.

## Padding

### Attacks against plain RSA
There are a number of attacks against plain RSA as described below.

- When encrypting with low encryption exponents (e.g., e = 3) and small values of the m (i.e., m < n1/e), the result of me is strictly less than the modulus n. In this case, ciphertexts can be decrypted easily by taking the eth root of the ciphertext over the integers.
- If the same clear-text message is sent to e or more recipients in an encrypted way, and the receivers share the same exponent e, but different p, q, and therefore n, then it is easy to decrypt the original clear-text message via the Chinese remainder theorem. Johan Håstad noticed that this attack is possible even if the clear texts are not equal, but the attacker knows a linear relation between them.[23] This attack was later improved by Don Coppersmith (see Coppersmith's attack).
- Because RSA encryption is a deterministic encryption algorithm (i.e., has no random component) an attacker can successfully launch a chosen plaintext attack against the cryptosystem, by encrypting likely plaintexts under the public key and test whether they are equal to the ciphertext. A cryptosystem is called semantically secure if an attacker cannot distinguish two encryptions from each other, even if the attacker knows (or has chosen) the corresponding plaintexts. RSA without padding is not semantically secure.
- RSA has the property that the product of two ciphertexts is equal to the encryption of the product of the respective plaintexts. That is, m1em2e ≡ (m1m2)e (mod n). Because of this multiplicative property, a chosen-ciphertext attack is possible. E.g., an attacker who wants to know the decryption of a ciphertext c ≡ me (mod n) may ask the holder of the private key d to decrypt an unsuspicious-looking ciphertext c′ ≡ cre (mod n) for some value r chosen by the attacker. Because of the multiplicative property, c′ is the encryption of mr (mod n). Hence, if the attacker is successful with the attack, they will learn mr (mod n), from which they can derive the message m by multiplying mr with the modular inverse of r modulo n.[citation needed]
- Given the private exponent d, one can efficiently factor the modulus n = pq. And given factorization of the modulus n = pq, one can obtain any private key (d′, n) generated against a public key (e′,  n).

### Padding schemes
To avoid these problems, practical RSA implementations typically embed some form of structured, randomized padding into the value m before encrypting it. This padding ensures that m does not fall into the range of insecure plaintexts, and that a given message, once padded, will encrypt to one of a large number of different possible ciphertexts.

Standards such as PKCS#1 have been carefully designed to securely pad messages prior to RSA encryption. Because these schemes pad the plaintext m with some number of additional bits, the size of the un-padded message M must be somewhat smaller. RSA padding schemes must be carefully designed so as to prevent sophisticated attacks that may be facilitated by a predictable message structure. Early versions of the PKCS#1 standard (up to version 1.5) used a construction that appears to make RSA semantically secure. However, at Crypto 1998, Bleichenbacher showed that this version is vulnerable to a practical adaptive chosen-ciphertext attack. Furthermore, at Eurocrypt 2000, Coron et al.[26] showed that for some types of messages, this padding does not provide a high enough level of security. Later versions of the standard include Optimal Asymmetric Encryption Padding (OAEP), which prevents these attacks. As such, OAEP should be used in any new application, and PKCS#1 v1.5 padding should be replaced wherever possible. The PKCS#1 standard also incorporates processing schemes designed to provide additional security for RSA signatures, e.g. the Probabilistic Signature Scheme for RSA (RSA-PSS).

Secure padding schemes such as RSA-PSS are as essential for the security of message signing as they are for message encryption. Two USA patents on PSS were granted (U.S. Patent 6,266,771 and U.S. Patent 7,036,014); however, these patents expired on 24 July 2009 and 25 April 2010 respectively. Use of PSS no longer seems to be encumbered by patents.[original research?] Note that using different RSA key pairs for encryption and signing is potentially more secure.

## Security and practical considerations

### Using the Chinese remainder algorithm
For efficiency, many popular crypto libraries (such as [OpenSSL](https://en.wikipedia.org/wiki/OpenSSL), [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) and [.NET](https://en.wikipedia.org/wiki/.NET_Framework)) use for decryption and signing the following optimization based on the Chinese remainder theorem. The following values are precomputed and stored as part of the private key:

![base](rsa.PNG)

These values allow the recipient to compute the exponentiation m = cd (mod pq) more efficiently as follows:

![base2](rsac.PNG)

This is more efficient than computing exponentiation by squaring, even though two modular exponentiations have to be computed. The reason is that these two modular exponentiations both use a smaller exponent and a smaller modulus.

### Integer factorization and RSA problem
See also: [RSA Factoring Challenge](https://en.wikipedia.org/wiki/RSA_Factoring_Challenge), [Integer factorization records](https://en.wikipedia.org/wiki/Integer_factorization_records), and [Shor's algorithm](https://en.wikipedia.org/wiki/Shor%27s_algorithm)

The security of the RSA cryptosystem is based on two mathematical problems: the problem of factoring large numbers and the RSA problem. Full decryption of an RSA ciphertext is thought to be infeasible on the assumption that both of these problems are hard, i.e., no efficient algorithm exists for solving them. Providing security against partial decryption may require the addition of a secure padding scheme.

The RSA problem is defined as the task of taking eth roots modulo a composite n: recovering a value m such that c ≡ me (mod n), where (n, e) is an RSA public key, and c is an RSA ciphertext. Currently the most promising approach to solving the RSA problem is to factor the modulus n. With the ability to recover prime factors, an attacker can compute the secret exponent d from a public key (n, e), then decrypt c using the standard procedure. To accomplish this, an attacker factors n into p and q, and computes lcm(p − 1, q − 1) that allows the determination of d from e. No polynomial-time method for factoring large integers on a classical computer has yet been found, but it has not been proven that none exists; see integer factorization for a discussion of this problem.

Multiple polynomial quadratic sieve (MPQS) can be used to factor the public modulus n.

The first RSA-512 factorization in 1999 used hundreds of computers and required the equivalent of 8,400 MIPS years, over an elapsed time of approximately seven months. By 2009, Benjamin Moody could factor an 512-bit RSA key in 73 days using only public software (GGNFS) and his desktop computer (a dual-core Athlon64 with a 1,900 MHz CPU). Just less than 5 gigabytes of disk storage was required and about 2.5 gigabytes of RAM for the sieving process.

Rivest, Shamir, and Adleman noted that Miller has shown that – assuming the truth of the extended Riemann hypothesis – finding d from n and e is as hard as factoring n into p and q (up to a polynomial time difference). However, Rivest, Shamir, and Adleman noted, in section IX/D of their paper, that they had not found a proof that inverting RSA is as hard as factoring.

As of 2020, the largest publicly known factored RSA number had 829 bits (250 decimal digits, RSA-250).[32] Its factorization, by a state-of-the-art distributed implementation, took approximately 2700 CPU years. In practice, RSA keys are typically 1024 to 4096 bits long. In 2003, RSA Security estimated that 1024-bit keys were likely to become crackable by 2010. As of 2020, it is not known whether such keys can be cracked, but minimum recommendations have moved to at least 2048 bits. It is generally presumed that RSA is secure if n is sufficiently large, outside of quantum computing.

If n is 300 bits or shorter, it can be factored in a few hours in a personal computer, using software already freely available. Keys of 512 bits have been shown to be practically breakable in 1999, when RSA-155 was factored by using several hundred computers, and these are now factored in a few weeks using common hardware. Exploits using 512-bit code-signing certificates that may have been factored were reported in 2011. A theoretical hardware device named TWIRL, described by Shamir and Tromer in 2003, called into question the security of 1024-bit keys.

In 1994, Peter Shor showed that a quantum computer – if one could ever be practically created for the purpose – would be able to factor in polynomial time, breaking RSA; see Shor's algorithm.

### Faulty key generation
See also: [Coppersmith's attack](https://en.wikipedia.org/wiki/Coppersmith%27s_attack) and [Wiener's attack](https://en.wikipedia.org/wiki/Wiener%27s_attack)

Finding the large primes p and q is usually done by testing random numbers of the correct size with probabilistic primality tests that quickly eliminate virtually all of the nonprimes.

The numbers p and q should not be "too close", lest the Fermat factorization for n be successful. If p − q is less than 2n1/4 (n = p⋅q, which even for "small" 1024-bit values of n is 3×1077), solving for p and q is trivial. Furthermore, if either p − 1 or q − 1 has only small prime factors, n can be factored quickly by Pollard's p − 1 algorithm, and hence such values of p or q should be discarded.

It is important that the private exponent d be large enough. Michael J. Wiener showed that if p is between q and 2q (which is quite typical) and d < n1/4/3, then d can be computed efficiently from n and e.

There is no known attack against small public exponents such as e = 3, provided that the proper padding is used. Coppersmith's attack has many applications in attacking RSA specifically if the public exponent e is small and if the encrypted message is short and not padded. 65537 is a commonly used value for e; this value can be regarded as a compromise between avoiding potential small-exponent attacks and still allowing efficient encryptions (or signature verification). The NIST Special Publication on Computer Security (SP 800-78 Rev. 1 of August 2007) does not allow public exponents e smaller than 65537, but does not state a reason for this restriction.

In October 2017, a team of researchers from Masaryk University announced the ROCA vulnerability, which affects RSA keys generated by an algorithm embodied in a library from Infineon known as RSALib. A large number of smart cards and trusted platform modules (TPM) were shown to be affected. Vulnerable RSA keys are easily identified using a test program the team released.

### Importance of strong random number generation
A cryptographically strong random number generator, which has been properly seeded with adequate entropy, must be used to generate the primes p and q. An analysis comparing millions of public keys gathered from the Internet was carried out in early 2012 by Arjen K. Lenstra, James P. Hughes, Maxime Augier, Joppe W. Bos, Thorsten Kleinjung and Christophe Wachter. They were able to factor 0.2% of the keys using only Euclid's algorithm.

They exploited a weakness unique to cryptosystems based on integer factorization. If n = pq is one public key, and n′ = p′q′ is another, then if by chance p = p′ (but q is not equal to q′), then a simple computation of gcd(n, n′) = p factors both n and n′, totally compromising both keys. Lenstra et al. note that this problem can be minimized by using a strong random seed of bit length twice the intended security level, or by employing a deterministic function to choose q given p, instead of choosing p and q independently.

Nadia Heninger was part of a group that did a similar experiment. They used an idea of Daniel J. Bernstein to compute the GCD of each RSA key n against the product of all the other keys n′ they had found (a 729-million-digit number), instead of computing each gcd(n, n′) separately, thereby achieving a very significant speedup, since after one large division, the GCD problem is of normal size.

Heninger says in her blog that the bad keys occurred almost entirely in embedded applications, including "firewalls, routers, VPN devices, remote server administration devices, printers, projectors, and VOIP phones" from more than 30 manufacturers. Heninger explains that the one-shared-prime problem uncovered by the two groups results from situations where the pseudorandom number generator is poorly seeded initially, and then is reseeded between the generation of the first and second primes. Using seeds of sufficiently high entropy obtained from key stroke timings or electronic diode noise or atmospheric noise from a radio receiver tuned between stations should solve the problem.

Strong random number generation is important throughout every phase of public-key cryptography. For instance, if a weak generator is used for the symmetric keys that are being distributed by RSA, then an eavesdropper could bypass RSA and guess the symmetric keys directly.

### Timing attacks
Kocher described a new attack on RSA in 1995: if the attacker Eve knows Alice's hardware in sufficient detail and is able to measure the decryption times for several known ciphertexts, Eve can deduce the decryption key d quickly. This attack can also be applied against the RSA signature scheme. In 2003, Boneh and Brumley demonstrated a more practical attack capable of recovering RSA factorizations over a network connection (e.g., from a Secure Sockets Layer (SSL)-enabled webserver). This attack takes advantage of information leaked by the Chinese remainder theorem optimization used by many RSA implementations.

One way to thwart these attacks is to ensure that the decryption operation takes a constant amount of time for every ciphertext. However, this approach can significantly reduce performance. Instead, most RSA implementations use an alternate technique known as cryptographic blinding. RSA blinding makes use of the multiplicative property of RSA. Instead of computing cd (mod n), Alice first chooses a secret random value r and computes (rec)d (mod n). The result of this computation, after applying Euler's theorem, is rcd (mod n), and so the effect of r can be removed by multiplying by its inverse. A new value of r is chosen for each ciphertext. With blinding applied, the decryption time is no longer correlated to the value of the input ciphertext, and so the timing attack fails.

### Adaptive chosen-ciphertext attacks
In 1998, Daniel Bleichenbacher described the first practical adaptive chosen-ciphertext attack against RSA-encrypted messages using the PKCS #1 v1 padding scheme (a padding scheme randomizes and adds structure to an RSA-encrypted message, so it is possible to determine whether a decrypted message is valid). Due to flaws with the PKCS #1 scheme, Bleichenbacher was able to mount a practical attack against RSA implementations of the Secure Sockets Layer protocol and to recover session keys. As a result of this work, cryptographers now recommend the use of provably secure padding schemes such as Optimal Asymmetric Encryption Padding, and RSA Laboratories has released new versions of PKCS #1 that are not vulnerable to these attacks.

A variant of this attack, dubbed "BERserk", came back in 2014. It impacted the Mozilla NSS Crypto Library, which was used notably by Firefox and Chrome.

### Side-channel analysis attacks
A side-channel attack using branch-prediction analysis (BPA) has been described. Many processors use a branch predictor to determine whether a conditional branch in the instruction flow of a program is likely to be taken or not. Often these processors also implement simultaneous multithreading (SMT). Branch-prediction analysis attacks use a spy process to discover (statistically) the private key when processed with these processors.

Simple Branch Prediction Analysis (SBPA) claims to improve BPA in a non-statistical way. In their paper, "On the Power of Simple Branch Prediction Analysis",[44] the authors of SBPA (Onur Aciicmez and Cetin Kaya Koc) claim to have discovered 508 out of 512 bits of an RSA key in 10 iterations.

A power-fault attack on RSA implementations was described in 2010.[45] The author recovered the key by varying the CPU power voltage outside limits; this caused multiple power faults on the server.

### Tricky implementation
There are many details to keep in mind in order to implement RSA securely (strong PRNG, acceptable public exponent...). This makes the implementation challenging, to the point the book Practical Cryptography With Go suggests avoiding RSA if possible.

## Implementations
Some cryptography libraries that provide support for RSA include:

- [Botan](https://en.wikipedia.org/wiki/Botan_(programming_library))
- [Bouncy Castle](https://en.wikipedia.org/wiki/Bouncy_Castle_(cryptography))
- [cryptlib](https://en.wikipedia.org/wiki/Cryptlib)
- [Crypto++](https://en.wikipedia.org/wiki/Crypto%2B%2B)
- [Libgcrypt](https://en.wikipedia.org/wiki/Libgcrypt)
- [Nettle](https://en.wikipedia.org/wiki/Nettle_(cryptographic_library))
- [OpenSSL](https://en.wikipedia.org/wiki/OpenSSL)
- [wolfCrypt](https://en.wikipedia.org/wiki/WolfSSL#wolfCrypt)
- [GnuTLS](https://en.wikipedia.org/wiki/GnuTLS)
- [mbed TLS](https://en.wikipedia.org/wiki/Mbed_TLS)
- [LibreSSL](https://en.wikipedia.org/wiki/LibreSSL)


## See also
- [Acoustic cryptanalysis](https://en.wikipedia.org/wiki/Acoustic_cryptanalysis)
- [Computational complexity theory](https://en.wikipedia.org/wiki/Computational_complexity_theory)
- [Cryptographic key length](https://en.wikipedia.org/wiki/Cryptographic_key_length)
- [Diffie–Hellman key exchange](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)
- [Key exchange](https://en.wikipedia.org/wiki/Key_exchange)
- [Key management](https://en.wikipedia.org/wiki/Key_management)
- [Elliptic-curve cryptography](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography)
- [Public-key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography)
- [Trapdoor function](https://en.wikipedia.org/wiki/Trapdoor_function)
