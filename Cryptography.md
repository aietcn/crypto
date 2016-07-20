# Cryptography

## Summary 

See [mind map of cryptography](crypt_brief.pdf).

### Famous Cyber Security Companies

See [here](http://cybersecurityventures.com/cybersecurity-500/)

### Known Attacks

- Hash Function: [wikipedia](https://en.wikipedia.org/wiki/Hash_function_security_summary)
- TLS: [wikipedia](https://en.wikipedia.org/wiki/Transport_Layer_Security)

## Symbols and Expressions

Symbol | Explanation
----|----
A | Alice, the sender/initializer
B | Bob, the receiver
T | Trust, a trusted agent or server
Z | the unknown Z
K | Key, for encryption/decryption/signature
M | Message, for communication
Ma | Message from Alice
UA | public key of Alice
IA | private key of Alice
IAM | message encrypted with Alice's public key
IAK | a special key encrypted with Alice's public key
IAMb | message from Bob encrypted with Alice's public key

Expression | Explanation
----|----
`>:` | operation of Alice
`<:` | operation of Bob
`=:` | operation of Trust
`≠:` | operation of Z
`.` | data fetching/saving
`+` | encryption/signing
`-` | decryption/verisigning
`]` | message sending
`x` | interception
function(arg) -> result | `function` with `arg` as input generates `result`, which is used as an alias for convenience afterwards
func1(arg1).func2(arg2) > output | after a series of operations, we get an `output` 

### Man in the Middle Attack

> a man-in-the-middle attack can defeat any protocol that doesn’t involve a secret of some kind.

## Key Exchange

- Symmetric Crypto

            >: . fetchFrom(Trust).forSessionKeyWith(B) > IAK, IBK
            >: - decrypt(IAK).withKey(IA) > K
            >: ] sendTo(B).with(IBK)
            
            <: - decrypt(IBK).withKey(IB) > K
            // communication begins

- Public-key Crypto

            >: . fetchFrom(DB).forPubilcKeyOf(B) > UB
            >: + encrypt(A.rand()->K).withKey(UB) > IBK
            >: ] sendTo(B)
            
            <: - decrypt(IBK).withKey(IB) > K
            // communication begins
            
            
    **MitM from Z**
    
            >: ] sendTo(B).with(UA)
            ≠: x itcpt(UA)
            ≠: ] sendTo(B).with(UZ)
            
            <: ] sendTo(A).with(UB)
            ≠: x itcpt(UB)
            ≠: ] sendTo(A).with(UZ)
            
            >: + encrypt(M).withKey(UZ) > IZM
            >: ] sendTo(B).with(IZM)
            ≠: x itcpt(IZM)
            ≠: - decrypt(IZM).withKey(UZ) > M
            ≠: + encrypt(M').withKey(UA) > IAM'
            ≠: ] sendTo(B).with(IAM')
            
            <: - decrypt(IAM').withKey(IA) > M'
            
    - Interlock

            >: ] sendTo(B).with(UA)
            
            <: ] sendTo(A).with(UB)
            
            >: + encrypt(Ma).withKey(UB) > IBMa
            >: ] sendTo(B).with(IBMa_first_half)
            
            <: . save(IBMa_first_half)
            <: + encrypt(Mb).withKey(UA) > IAMb
            <: ] sendTo(A).with(IAMb_first_half)
            
            >: . save(IAMb_first_half)
            >: ] sendTo(B).with(IBMa_second_half)

            <: . merge(IBMa_first_half).with(IBMa_second_half) > IBMa
            <: - decrypt(IBMa).withKey(IB) > Ma
            <: ] sendTo(A).with(IAMb_second_half)
            
            >: . merge(IAMb_first_half).with(IAMb_second_half) > IAMb
            >: - decrypt(IAMb).withKey(IA) > Mb
            
        half of message could be replaced with blocks;
        first half of the message could be one-way hash function, with 
        encrypted message being the second half.


- Key and Message Transmission

            >: + encrypt(M).withKey(A.rand()->K) > IM
            >: . fetchFrom(DB).forPublicKeyOf(B) > UB
            >: + encrypt(K).withKey(UB) > IK
            >: ] sendTo(B).with(IM).and(IK)
            // >: sendTo(B).with(IM).and(IK).signedWith(IA) > SA
            
            // <: verifySign(SA)
            <: - decrypt(IK).withKey(IB) > K
            <: - decrypt(IM).withKey(K) > M

- Key and Message Broadcast

    use `Key and Message Transmission` protocol for separate listeners


## Authentication

- public-key

            >: + encrypt(A.rand()).withKey(IA) > IK
            >: ] sendTo(host).with(IK)
            
            <: ] sendTo(A).with(host.rand()->HK)
            
            >: + encrypt(K, HK).withKey(IA) > IK'
            >: ] sendTo(host).with(IK')
            
### Authentication with key exchange

- Wide-Mouth Frog (symmetric key management with a trusted server)

            >: + encrypt(timestamp,B,SK).withKey(Ka) > Ma
            >: ] sendTo(Trust).with(Ma)
            
            =: - decrypt(M).withKey(Ka) > timestamp, B, SK
            =: + encrypt(timestamp,A,SK).withKey(Kb) > Mb
            =: ] sendTo(B).with(Mb)
            
- Yahalom

            >: ] sendTo(B).with(A,A.rand()->Ra)
            
            <: + encrypt(A,Ra,B.rand()->Rb).withKey(Kb) > Mb
            <: ] sendTo(Trust).with(Mb)
            
            =: - decrypt(Mb).withKey(Kb) > A,Ra,Rb
            =: + encrypt(B,Ra,Rb,SK).withKey(Ka) > M'a
            =: + encrypt(A,SK).withKey(Kb) > M'b
            =: ] sendTo(A).with(M'a,M'b)
            
            >: - decrypt(M'a).withKey(Ka) > Ra, Rb, SK
            >: + encrypt(Rb).withKey(SK) > RbM
            >: ] sendTo(B).with(M'b).and(RbM)
            
            <: - decrypt(M'b).withKey(Kb) > A, SK
            <: - decrypt(RbM).with(SK) > Rb
            
- Needham-Schroeder (symmetric cryptography with trusted server)

            >: ] sendTo(Trust).with(A,B,A.rand()->Ra)
            
            =: + encrypt(SK, A).withKey(Kb) > Mb
            =: + encrypt(Ra, B, SK).withKey(Ka) > Ma
            =: ] sendTo(A).with(Ma, Mb)
            
            >: - decrypt(Ma).withKey(Ka) > Ra, B, SK, Mb
            >: ] sendTo(B).with(Mb)
            
            <: - decrypt(Mb).withKey(Kb) > SK, A
            <: + encrypt(B.rand()->Rb).withKey(SK) > M'b
            <: ] sendTo(A).with(M'b)
            
            // prevent replay attacks
            >: - decrypt(M'b).withKey(SK) > Rb
            >: + encrypt(Rb-1).withKey(SK) > M'a
            
            <: - decrypt(M'a).withKey(SK) > Rb-1

- Otway-Rees (an optimized version of Needham-Schroeder)

            >: + encrypt(A,B,A.rand()->Ra,A.index()->Ia).withKey(Ka) > Ma
            >: ] sendTo(B).with(Ia,A,Ma)
            
            <: + encrypt(B,A,B.rand()->Rb,Ia).withKey(Kb) > Mb
            <: ] sendTo(Trust).with(Mb,Ma,Ia,B,A)
            
            =: - decrypt(Ma).withKey(Ka) > Ia, Ra, A
            =: - decrypt(Mb).withKey(Kb) > Ia, Rb, B
            =: + encrypt(Ra,SK).withKey(Ka) > M'a
            =: + encrypt(Rb,SK).withKey(Kb) > M'b
            =: ] sendTo(B).with(Ia,M'a,M'b)
            
            <: - decrypt(M'b).withKey(Kb) > Rb, SK
            <: ] sendTo(A).with(M'a, Ia)
            
            >: - decrypt(M'a).withKey(Ka) > Ra, SK

- Kerberos (a variant of Needham-Schroeder, requires clock sync)

            >: ] sendTo(Trust).with(A,B)
            
            =: + encrypt(A,EXP,timestamp,SK).withKey(Ka) > Ma
            =: + encrypt(B,EXP,timestamp,SK).withKey(Kb) > Mb
            =: ] sendTo(A).with(Ma,Mb)
            
            >: - decrypt(Ma).withKey(Ka) > timestamp,SK
            >: + encrypt(M).withKey(SK) > KM
            >: ] sendTo(B).with(KM, Mb)
            
            <: - decrypt(Mb).withKey(Kb) > timestamp,SK
            <: - decrpyt(KM).withKey(SK) > M
            <: ] sendTo(A).with(encrypt(M',timestamp+1).withKey(SK))
            
- Neuman-Stubblebine

            >: ] sendTo(B).with(A,A.rand()->Ra)
            
            <: + encrypt(A,Ra,timestamp).withKey(Kb) > Mb
            <: ] sendTo(Trust).with(Mb,B.rand()->Rb,B)
            
            =: - decrypt(Mb).withKey(Kb) > Ra,timestamp,A,B,Rb
            =: + encrypt(B,Ra,SK,timestamp).withKey(Ka) > M'a
            =: + encrypt(A,SK,timestamp).withKey(Kb) > M'b
            =: ] sendTo(A).with(M'a,M'b,Rb)
            
            >: - decrypt(M'a).withKey(Ka) > Ra,SK,timestamp
            >: + encrypt(Rb).withKey(SK) > KM
            >: ] sendTo(B).with(KM,M'b)
            
            <: - decrypt(M'b).withKey(Kb) > SK,timestamp
            <: - decrypt(KM).withKey(SK) > Rb


## Real world practice

### Authentication Schemes

#### Password and Secret Key Schemes

##### One-time Password(OTP)

- [S/Key](https://en.wikipedia.org/wiki/S/KEY);

- [Secret Token](https://en.wikipedia.org/wiki/Security_token)

### ISDN

- Calling

            >: + encrypt(Ka).with(Pa)
            >: ] initCall(B)
            
            >< Y exchangeSessionKeyUsing(UA,UB) > SK
            
            >: + sign(Cert,AUTH).withKey(NetK) > NetSA
            >: ] sendTo(B).with(ACert,BAUTH,NetSA)
            
            <: - verifySign(NetSA).of(ACert,AAUTH).withKey(NetK)
            
            <> Y challengeReplyFor(A.PhoneK, UA)
            
            <: . ring(B.phoner)
            <: . authenticate(B)
            <: + sign(BCert,BAUTH).withKey(NetK) > NetSB
            <: ] sendTo(A).with(BCert,BAUTH,NetSB)
            
            >: - verifySign(NetSB).of(BCert,BAUTH).withKey(NetK)

            <> Y challengeReplyFor(B.PhoneK, UB)
            
            <><><>
            
            >< . hangPhone(A,B).thenDel(SK,ACert,BCert)

### Kerberos: a single,powerful,hard to implement KDC

- TGS: Ticket Granting Service, and Authenticator

            >: ] requestTicket(Srv).from(TGS)
            
            =: + encrypt(C,CADDR,VLD,SK).withKey(ISrv) > Tkt
            =: + encrypt(Tkt).withKey(IC) > ITkt
            =: ] sendTo(C).with(ITkt)
            
            >: - decrypt(ITkt).withKey(IC) > Tkt
            >: + encrypt(C,timestamp,?).withKey(SK) > AUTH
            >: ] requestAccess(Srv).with(AUTH,Tkt)
            
            <: - decrypt(Tkt).withKey(ISrv) > C,CADDR,VLD,SK
            <: - decrypt(AUTH).withKey(SK) > timestamp, C, ?

#### For Version 5

- Initial Ticket

            >: ] sendTo(KBRS).with(C)
            
            =: . checkExistence(C).in(DB)
            =: + encrypt(gen(SK).for(TGS)->STGT).withKey(IC) > ISTGT
            =: + encrypt(gen(SK).for(C)->CTGT).withKey(ITGS) > ICTGT
            =: ] sendTo(C).with(ISTGT, ICTGT)
            
            >: - decrypt(ISTGT).withKey(IC) > STGT


#### Drawbacks

    a single KDC with large table storing all public keys and generate session keys for all.
    
- KDC could be a SPoF, bottlenech of the network

### PEM

### PGP

**Web of Trust**

Everyone can generate,distribute self-own keys, choose to trust whoever it want to trust

### Smart Cards

### SSL

- How to manage public keys?

CA: issue and store `principle : public-key`, sign public-keys with CA's private-key for verification

- How to make CA scalable?

distribute public-keys of CAs in clients - in storage

- How to revoke public keys?

    - CRL(certificate revokation list: this could be a large un-maintaineble list)
    
    - OCSP(online certificate status protocol: latency, SPoF)
    - built into clients (browsers, systems)

    CA-signed certificates valid through a long time.

- Key Generation

            >: session key initiated by A
            <: B take part in sesison key generation with nonce
            >: A sign the session key

**Web Protection**

- Data on network: SSL/TLS
- Code/data in browser: HTTPS(certificate for hostname, cookies, )
- UI: lock icon + URL 
- DNS hijacking: 

### Wechat

- Authentication

            >: ] sendTo(B).with(A,A.rand()->T,K)[https]
            
            <: + sha1(T,B.rand()->Rb,timestamp) > Sb
            <: ] sendTo(A).with(Sb, Mb)
            
            >: - verifySign(Sb).with(T,Rb,timestamp)
            >: ] sendTo(B).with(Mb)

- Message Response

            // secure mode
            <: + encrypt(M).withKey(K) > KM
            <: ] sendTo(A).with(KM)

            >: - decrypt(KM).withKey(K) > M
            
- Access Token (**Vulnerable to MitM attack**)

            >: ] sendTo(B).with(A, Ka)
            
            <: ] sendTo(A).with(accessToken, expire)




            
