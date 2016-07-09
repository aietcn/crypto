# Cryptography

## Symbols and Expressions

Symbol | Explanation
----|----
A | Alice, the sender/initializer
B | Bob, the receiver
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
`=:` | operation of Z
`.` | data fetching/saving
`+` | encryption/signing
`-` | decryption/verisigning
`]` | message sending
`x` | interception
function(arg) -> result | `function` with `arg` as input generates `result`, which is used as an alias for convenience afterwards
func1(arg1).func2(arg2) > output | after a series of operations, we get an `output` 



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
            =: x itcpt(UA)
            =: ] sendTo(B).with(UZ)
            
            <: ] sendTo(A).with(UB)
            =: x itcpt(UB)
            =: ] sendTo(A).with(UZ)
            
            >: + encrypt(M).withKey(UZ) > IZM
            >: ] sendTo(B).with(IZM)
            =: x itcpt(IZM)
            =: - decrypt(IZM).withKey(UZ) > M
            =: + encrypt(M').withKey(UA) > IAM'
            =: ] sendTo(B).with(IAM')
            
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

- One-way hash
