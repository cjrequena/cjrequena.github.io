---
layout: post
title: "[CRYPTOGRAPHY] Cryptography Foundations"
author: "Carlos Requena"
lang: en
ref: cryptography-foundations
tags:
   - Cryptography
---

<div style="text-align:center"><span style="color:red;font-weight: bold">"It is not knowledge, but the act of learning, not possession but the act of getting there, which grants the greatest enjoyment." --Carl Friedrich Gauss
</span></div>

# Cryptography Foundations
**Cryptography** is a field of computer science and mathematics that focusses on techniques for secure communication between two parties (Alice & Bob) while a third-party (Eve1 or Mallory)
is present (See figure below). This is based on methods like encryption, decryption, signing, generating of pseudo random numbers, etc.

![]({{ site.baseurl }}/assets/images/cryptography/cryptography-1.PNG)

**Cryptography** is the science of making information secure in the presence of adversaries. It provides a means of secure communication in the presence of adversaries with assumed 
limitless resources. Ciphers are used to encrypt data so that if intercepted by an adversary, the data is meaningless to them without decryption, which requires the secret key.

**Cryptography** provides various security services, such as Confidentiality, Integrity, Authentication, (Entity Authentication and Data origin authentication) and non-repudiation. 
Additionally, accountability is also required in various security systems.

- **Confidentiality** Defines a set of rules that limits access or adds restriction on certain information

- **Data Integrity** Takes care of the consistency and accuracy of data during its entire life-cycle.

- **Authentication** Confirms the truth of an attribute of a datum that is claimed to be true by some entity

- **Non-Repudiation** Ensures the inability of an author of a statement resp. a piece of information to deny it

There are in general two different schemes: On the one hand, there are **symmetric schemes**, where both, Alice and Bob, need to have the same key in order to encrypt their communication.
For this, they have to securely exchange the key initially. On the other hand, there also exists the concept of **asymmetric schemes** where Alice and Bob both have a private and a
public keys. The public key can be shared with anyone, so Bob can use it to encrypt a message for Alice. But only Alice, with the corresponding private key, can decrypt the encrypted
message from Bob.

Cryptography   algorithms   can   be   classified   into   three   board   categories,   **asymmetric   (public-key)   cryptosystem**,   **symmetric  (secret-key)  cryptosystem**  and  **hash  functions.**

In   general,   Cryptography   protocol   employs  asymmetric  cryptosystem  to  exchange  the  secret  key  and  then  uses  faster  secret  key  algorithms  to  ensure  confidentiality   of
the   data   stream. Symmetric   cryptosystem  (Secret-key  algorithm)  is  used  to  encrypt  and  decrypt messages by using the same secret key. While hash functions  are  a  non-public  key
cryptography  and  work  without  key.  As  well  hash  functions  are  normally  used  as  data  integrity  primitive  in  more  complicated  cryptographic  protocols.

## Confidentiality

When we talk about confidentiality of information, we are talking about protecting the information from disclosure to unauthorized parties. Confidentiality is the assurance that information
is only available to authorized entities.

Information has value, especially in today’s world. Bank account statements, personal information, credit card numbers, trade secrets, government
documents. Every one has information they wish to keep a secret. Protecting such information is a very major part of information security.

A very key component of protecting information confidentiality would be encryption. Encryption ensures that only the right people (people who knows the key)
can read the information. Encryption is VERY widespread in today’s environment and can be found in almost every major protocol in use. A very prominent
example will be SSL/TLS, a security protocol for communications over the internet that has been used in conjunction with a large number of internet protocols
to ensure security.

Other ways to ensure information confidentiality include enforcing file permissions and access control list to restrict access to sensitive information.

## Integrity

Integrity of information refers to protecting information from being modified by unauthorized parties. Integrity is the assurance that information is modifiable only by authorized entities.

Information only has value if it is correct. Information that has been tampered with could prove costly. For example, if you were sending an online money transfer for $100, but the information
was tampered in such a way that you actually sent $10,000, it could prove to be very costly for you.

As with data confidentiality, cryptography plays a very major role in ensuring data integrity. Commonly used methods to protect data integrity includes hashing the data you receive and comparing
it with the hash of the original message. However, this means that the hash of the original data must be provided to you in a secure fashion. More convenient methods would be to use existing schemes
such as GPG to digitally sign the data.

## Authentication

Authentication is a service used to provide the identity of an entity.

### Entity authentication

Entity authentication is a technique designed to let one party prove the identity of another party. An entity can be a person, a process,a client, or a server.
The entity whose identity needs to be proved is called the claimant; the party that tries to prove the identity of the claimant is called the verifier.

### Data origin authentication

Use Data Origin Authentication, enabling the recipient to verify that messages have not been tampered with in transit (Data Integrity) and that they originate from the expected sender (Authenticity).

You can also use Digital Signatures to provide evidence that a subject has performed a particular action related to data, in cases where the subject denies performing the action (non-repudiation).

Simply authenticates one message; the process needs to be repeated for each new message.

## Non-repudiation

Non-repudiation  is  a  service  used  to  confirm  the  involvement  of  an  entity  in  a  certain  form  communication,  and  prevents  any  party  from  denying  the sent message.

## Accessibility

Accessibility  is  a  service  put  in  place  to  allow  the  use  of  information  resources  by  authorized  entities.

## Accountability

Accountability is the assurance that actions affecting security can be traced to the responsible party. This is usually provided by logging and audit mechanisms in systems where a
detailed audit is required due to the nature of the business, for example, in electronic trading systems. Detailed logs are vital to trace an entity's actions, for example, when a trade is placed
in an audit record with the date and time stamp and the entity's identity is generated and saved in the log file. This log file can optionally be encrypted and can be part of the database or a
standalone ASCII text log file on a system.


# Mathematics

## Set
A set is a collection of distinct objects, for example, X= {1, 2, 3, 4, 5}.

## Group
A group is a commutative set with one operation that combines two elements of the set. The group operation is closed and associated with an identity element defined. 

A group in mathematics is a set for which we have defined a binary operation that we call "addition" and indicate with the symbol +. In order for the set G
 
**To be a group, addition must defined so that it respects the following four properties:**

1. **closure:** if a and b are members of G, then a+b is a member of G; Closure (closed) means that if, for example, elements A and B are in the set, then the resultant element 
after performing  operation on the  elements is also in the set.

2. **associativity:** (a+b)+c=a+(b+c); Associative means that the grouping of elements does not affect the result of the operation. 

3. there exists an identity element 0 such that a+0=0+a=a; 

4. every element has an inverse, that is: for every a there exists b such that a+b=0; Each element in the set has an inverse.

If we add a fifth requirement:

5. **commutativity:** a+b=b+a, then the group is called abelian group.

With the usual notion of addition, the set of integer numbers Z is a group (moreover, it's an abelian group). 

The set of natural numbers N however is not a group, as the fourth property can't be satisfied.

Groups are nice because, if we can demonstrate that those four properties hold, we get some other properties for free. For example: the identity element is unique; also the 
inverses are unique, that is: for every a there exists only one b such that a+b=0 (and we can write b as −a). Either directly or indirectly, these and other facts about groups 
will be very important for us later.

## Field
A field is a set that contains both additive and multiplicative groups. More precisely, all elements in the set form an additive and multiplicative group. It satisfies specific 
axioms for addition and multiplication. For all group operations, the distributive law is also applied. The law dictates that the same sum or product will be produced even if 
any terms or factors are reordered.

## A finite field
A finite field is a field with a finite set of elements. Also known as Galois fields, these structures are of particular importance in cryptography as they can be used to produce 
accurate and error-free results of arithmetic operations. For example, prime finite fields are used in elliptic curve cryptography to construct discrete logarithm problem.

## Order
This is the number of elements in a field. It is also known as the cardinality of the field.

## Prime fields
This is a finite field with a prime number of elements. It has specific rules for addition and multiplication, and each nonzero element in the field has an inverse. Addition and 
multiplication operations are performed modulo p.

## Ring
If more than one operation can be defined over an abelian group, that group becomes a ring. There are also certain properties that need to be satisfied. A ring must have closure 
and associative and distributive properties.

## A cyclic group
A cyclic group is a type of group that can be generated by a single element called the group generator. In other words, if the group operation is repeatedly applied to a particular 
element in the group, then all elements in the group can be generated.

## An abelian group
An abelian group is formed when the operation on the elements of a set is commutative. Commutative law basically means that changing the order of the elements does not affect the 
result of the operation, for example, A X B = B X A.

## Modular arithmetic
Also known as clock arithmetic, numbers in modular arithmetic wrap around when they reach a certain fixed number. This fixed number is a positive number called modulus and all 
operations are performed with regard to this fixed number. In an analogy to a clock, there are number from 1 to 12. When it reaches 12, the number 1 starts again. In other words, 
this arithmetic deals with the remainders after the division operation. For example, 50 mod 11 is 6 because 50 / 11 leaves a remainder of 6.

# Symmetric cryptography

## What Is Symmetric Key Cryptography?

Symmetric key cryptography (or symmetric encryption) is a type of encryption scheme in which the same key is used both to encrypt and decrypt messages. Such a
method of encoding information has been largely used in the past decades to facilitate secret communication between governments and militaries. Nowadays,
symmetric key algorithms are widely applied in various types of computer systems to enhance data security.

## How does symmetric encryption work?

Symmetric encryption schemes rely on a single key that is shared between two or more users. The same key is used to encrypt and decrypt the so-called plaintext
(which represents the message or piece of data that is being encoded). The process of encryption consists of running a plaintext (input) through an encryption
algorithm called a cipher, which in turn generates a ciphertext (output).

If the encryption scheme is strong enough, the only way for a person to read or access the information contained in the ciphertext is by using the corresponding
key to decrypt it. The process of decryption is basically converting the ciphertext back to plaintext.

The security of symmetric encryption systems is based on how difficult it randomly guess the corresponding key to brute force them. A 128-bit key, for example,
would take billions of years to guess using common computer hardware. The longer the encryption key is, the harder it becomes to crack it. Keys that are
256-bits length are generally regarded as highly secure and theoretically resistant to quantum computer brute force attacks.

Two of the most common symmetric encryption schemes used today are based on block and stream ciphers. Block ciphers group data into blocks of predetermined
size and each block is encrypted using the corresponding key and encryption algorithm (e.g., 128-bit plaintext is encrypted into 128-bit ciphertext). On
the other hand, stream ciphers do not encrypt plaintext data by blocks, but rather by 1-bit increments (1-bit plaintext is encrypted into 1-bit ciphertext
at a time).

## Advantages and disadvantages of symmetric encryption

Symmetric algorithms provide a fairly high level of security while at the same time allowing for messages to be encrypted and decrypted quickly. The relative
simplicity of symmetric systems is also a logistical advantage, as they require less computing power than the asymmetric ones. In addition, the security
provided by symmetric encryption can be scaled up simply by increasing key lengths. For every single bit added to the length of a symmetric key, the
difficulty of cracking the encryption through a brute force attack increases exponentially.

While symmetric encryption offers a wide range of benefits, there is one major disadvantage associated with it: the inherent problem of transmitting the keys
used to encrypt and decrypt data. When these keys are shared over an unsecured connection, they are vulnerable to being intercepted by malicious third parties.
If an unauthorized user gains access to a particular symmetric key, the security of any data encrypted using that key is compromised. To solve this problem,
many web protocols use a combination of symmetric and asymmetric encryption to establish secure connections. Among the most prominent examples of such a hybrid
system is the Transport Layer Security (TLS) cryptographic protocol used to secure large portions of the modern internet.

It should also be noted that all types of computer encryption are subject to vulnerabilities due to improper implementation. While a sufficiently long key
can make a brute force attack mathematically impossible, errors in implementation made by programmers often create weaknesses that open up the way for cyber
attacks.

Thanks to its relative speed, simplicity, and security, symmetric encryption is used extensively in applications ranging from securing internet traffic to
protecting data stored on cloud servers. Although it is frequently paired with asymmetric encryption in order to solve the problem of safely transferring keys,
symmetric encryption schemes remain a critical component of modern computer security.

## Data Encryption Standard (DES)

DES was introduced by the US National Institute of Standards and Technology (NIST) as a standard algorithm for encryption and was in main use during 1980s and 1990s,
but it has been not proven to be very resistant against brute force attacks, due to advances in technology and cryptography research. Especially in July 1998,
Electronic Frontier Foundation (EFF) broke DES using a special purpose machine. DES uses a key of only 56 bits, which has raised some concerns.
This problem was addressed with the introduction of Triple DES (3DES), which proposed the usage of a 168-bit key using three 56-bit keys and the same number of
executions of the DES algorithm, thus making brute force attacks almost impossible. But other limitations, such as slow performance and 64-bit block size,
are not desirable.

## Advanced Encryption Standard (AES)

In 2001, after an open competition, an encryption algorithm named Rijndael that was invented by cryptographers Joan Daemen and Vincent Rijmen was standardized as AES
with minor modifications by NIST in 2001. So far, no attack has been found against AES that is better than the brute force method. Original Rijndael allows different
key and block sizes of 128-bit, 192-bit, and 256-bits, but in the AES standard, only a 128-bit block size is allowed. However, key sizes of 128-bit, 192-bit,
and 256-bit are allowed.

# Asymmetric cryptography

Public key cryptography (PKC), also known as asymmetric cryptography, is a framework that uses both a private and a public key, as opposed to the single key
used in symmetric cryptography. The use of key pairs gives PKC a unique set of characteristics and capabilities that can be utilized to solve challenges
inherent in other cryptographic techniques. This form of cryptography has become an important element of modern computer security, as well as a critical
component of the growing cryptocurrency ecosystem.

While symmetric encryption algorithms use the same key to perform both the encryption and decryption functions, an asymmetric encryption algorithm, by
contrast, uses one key to encrypt the data and another key to decrypt it. In asymmetric systems, the key used for encryption is known as the public key
and can be freely shared with others. On the other hand, the key used for decryption is the private key and should be kept in secret.

For example, if Alice sends Bob a message that is protected by symmetric encryption, she needs to share the same key she used for encryption with Bob so
that he can decrypt the message. This means that if a malicious actor intercepts the key, they are able to access the encrypted information.

However, if Alice uses an asymmetric scheme instead, she encrypts the message with Bob’s public key, so Bob will be able to decrypt it with his private key.
Thus, asymmetric encryption offers a higher level of security because even if someone intercepts their messages and finds Bob’s public key, they are not able
to decrypt the message.

## How does public key cryptography work?

In a PKC scheme, the public key is used by a sender to encrypt information, while the private key is used by a recipient to decrypt it. Because the two keys
are different from one another, the public key can be safely shared without compromising the security of the private one. Each asymmetric key pair is unique,
ensuring that a message encrypted using a public key can only be read by the person who possesses the corresponding private key.

Because asymmetric encryption algorithms generate key pairs that are mathematically linked, their key lengths are much longer than those used in symmetric
cryptography. This longer length - typically between 1,024 and 2,048 bits - makes it extremely difficult to compute a private key from its public counterpart.
One of the most commons algorithm for asymmetric encryption in use today is known as RSA.

In the RSA scheme, keys are generated using a modulus that is arrived at by multiplying two numbers (often two large prime numbers). In basic terms, the
modulus generates two keys (one public that can be shared, and one private that should be kept in secret). The RSA algorithm was first described in 1977 by
Rivest, Shamir, and Adleman (hence, RSA) and remains a major component of public key cryptography systems.

## Limitations of public key cryptography

Although it can be used to enhance computer security and provide verification of message integrity, PKC does have some limitations. Owing to the complex
mathematical operations involved in encryption and decryption, asymmetric algorithms can be quite slow when forced to deal with large amounts of data.
This type of cryptography also depends heavily on the assumption that the private key will remain secret. If a private key is accidentally shared or exposed,
the security of all messages that have been encrypted with its corresponding public key will be compromised. It is also possible for users to accidentally
lose their private keys, in which case it becomes impossible for them to access the encrypted data.

## Applications of public key cryptography

This type of cryptography is used by many modern computer systems to provide security for sensitive information. Emails, for example, can be encrypted using
public key cryptography techniques to keep their contents confidential.

The secure sockets layer (SSL) protocol that makes secure connections to websites possible also employs asymmetric cryptography. PKC systems have even been
explored as a means of providing a secure electronic voting environment that would potentially allow voters to participate in elections from their home
computers.

PKC also features prominently in blockchain and cryptocurrency technology. When a new cryptocurrency wallet is set up, a pair of keys is generated
(public and private keys). The wallet address is generated using the public key and can be securely shared with others. The private key, on the other hand,
is used for creating digital signatures and verifying transactions, and therefore, must be kept in secret. Once a transaction has been verified by confirming
the hash contained in the digital signature, that transaction can be added to the blockchain ledger. This system of digital signature verification ensures
that only the person who has the private key associated with the corresponding cryptocurrency wallet can release funds from it.

It should be noted that the asymmetric ciphers used in cryptocurrency applications are different from those used for computer security purposes. Bitcoin and
Ethereum, for instance, use a specialized cipher known as the Elliptic Curve Digital Signature Algorithm (ECDSA) to verify transactions.

From computer security to verifying cryptocurrency transactions, public key cryptography plays an important role in securing modern digital systems. By
using paired public and private keys, asymmetric cryptography algorithms resolve fundamental security concerns presented by symmetric ciphers. Although PKC
has been in use for many years, new uses and applications are regularly being developed for it, particularly in the blockchain and cryptocurrency space.

## Underlying mathematical of Public key cryptography

Public key cryptography algorithms are based on various underlying mathematical problems.

There are three main families of asymmetric algorithms.

### Integer factorization

These schemes are based on the fact that large integers are very hard to factor. RSA is the prime example of this type of algorithm.

### Discrete logarithm

This is based on a problem in modular arithmetic that it is easy to calculate the result of modulo function but it is computationally infeasible to find the
exponent of the generator. In other words, it is extremely difficult to find the input from the result. This is a one-way function.

For example, consider the following equation:

3^2 mod 10 = 9

Now given 9 finding 2, the exponent of the generator 3 is very hard. This hard problem is commonly used in Diffie-Hellman key exchange and digital signature algorithms.

### Elliptic curves

This is based on the discrete logarithm problem discussed earlier, but in the context of elliptic curves. Elliptic curve is an algebraic cubic curve over a field, which
can be defined by an equation shown here. The curve is non-singular, which means that it has no cusps or self-intersections. It has two variables a, b, along with a point of infinity.

Here, a, b are integers that can have various values and are elements of the field on which the elliptic curve is defined. Elliptic curves can be defined over reals, rational numbers,
complex numbers, or finite fields. For cryptographic purposes, elliptic curve over prime finite fields is used instead of real numbers. Additionally, the prime should be greater than 3.
Different curves can be generated by varying the value of a, b.

Mostly prominently used cryptosystems based on elliptic curves are Elliptic Curve Digital Signatures Algorithm (ECDSA) and Elliptic Curve Diffie-Hellman (ECDH) key exchange.

## RSA

RSA (Rivest–Shamir–Adleman) is one of the first public-key cryptosystems and is widely used for secure data transmission. In such a cryptosystem, the encryption key is public
and it is different from the decryption key which is kept secret (private). In RSA, this asymmetry is based on the practical difficulty of the factorization of the product of
two large prime numbers, the "factoring problem". The acronym RSA is made of the initial letters of the surnames of Ron Rivest, Adi Shamir, and Leonard Adleman, who first
publicly described the algorithm in 1977. Clifford Cocks, an English mathematician working for the British intelligence agency Government Communications Headquarters (GCHQ),
had developed an equivalent system in 1973, but this was not declassified until 1997.

A user of RSA creates and then publishes a public key based on two large prime numbers, along with an auxiliary value. The prime numbers must be kept secret. Anyone can use the
public key to encrypt a message, but only someone with knowledge of the prime numbers can decode the message. Breaking RSA encryption is known as the RSA problem. Whether it is
as difficult as the factoring problem remains an open question. There are currently no published methods to defeat the system if a large enough key is used.

RSA is a relatively slow algorithm, and because of this, it is less commonly used to directly encrypt user data. More often, RSA passes encrypted shared keys for symmetric key
cryptography which in turn can perform bulk encryption-decryption operations at much higher speed.

## Elliptic curve cryptography (ECC)

ECC is based on the discrete logarithm problem that is based on elliptic curves over finite fields (Galois fields). The main benefit of ECC over other types of public key
algorithms is that it needs a smaller key size while providing the same level of security as, for example, RSA.

Two notable schemes that originate from ECC are Elliptic Curve Diffie-Hellman (ECDH) for key exchange and Elliptic Curve Digital Signature Algorithm (ECDSA) for digital signatures.

It can also be used for encryption but is not usually used for this purpose in practice; instead, key exchange and digital signatures are more commonly used. As ECC needs less
space to operate, it is becoming very popular on embedded platforms or in systems where storage resources are limited. As a comparison, the same level of security can be achieved
in ECC by only using 256-bit operands as compared to 3072-bits in RSA.

## Referencias

- [An Introduction to Cryptography](https://www.mathematik.uni-kl.de/~ederc/download/Cryptography.pdf)
- [Mastering Blockchain](https://www.packtpub.com/big-data-and-business-intelligence/mastering-blockchain-second-edition)
- [Elliptic Curve Cryptography: a gentle introduction](https://andrea.corbellini.name/2015/05/17/elliptic-curve-cryptography-a-gentle-introduction/)
- [Confidentiality, Integrity, Availability: The three components of the CIA Triad](https://security.blogoverflow.com/2012/08/confidentiality-integrity-availability-the-three-components-of-the-cia-triad/)
- [Cryptography and network security](http://www.cs.siue.edu/~tgamage/S18/CS490/L/WK12.pdf)
- [Cryptography Based Authentication Methods](https://pdfs.semanticscholar.org/63f8/1e41181a9e32be8bee279a47b9343579b823.pdf)
- [Binance Academy - What Is Symmetric Key Cryptography?](https://www.binance.vision/security/what-is-symmetric-key-cryptography)
- [Binance Academy - What is Public Key Cryptography?](https://www.binance.vision/security/what-is-public-key-cryptography)
