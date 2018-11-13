# Icon Distributed Identifier
ICON[1,2,3] is a decentralized network that connects various independent communities to enable interoperability between them. ICON DID is a decentralized identifier devised to provide a way to uniquely identify a person, an organization, or a digital device across the communities connected to the ICON network. ICON DID method specification conforms to the DID and the DID Documents Spec[4]. This document describes how ICON blockchain manages the DIDs and the DID documents, and specifies a set of rules for how a DID is created, queried, updated, and revoked.   

## 1. ICON DID

### 1.1 ICON DID Method Name
The namestring that shall identify this DID method is: `icon`.

A DID that uses this method **MUST** begin with the following prefix: `did:icon`. Per the DID specification, this string MUST be in lowercase. The remainder of the DID, after the prefix, is specified below.

### 1.2 ICON DID Method Specific Identifier
The decentralized identifiers(DID) on ICON blockchain is of the following format:

```
icon-did = “did:icon:” + specific-idstring
specific-idstring = network-id +  idstring + checksum
network-id = 4*HEXDIG
idstring = 40*HEXDIG
checksum = 8*HEXDIG
```

### 1.3 Specific-idstring Generation Method
ICON DID should be created by each entity, and the DID must be unique within the ICON network. Specific-idstring is a hex encoded string and composed of the concatenation of the network-id, idstring and checksum. 

* network-id of the “mainnet” is defined as “0000”. Other values can be assigned to different networks in the future.
* idstring should be defined by the first 20 bytes in the 32-byte transaction hash of the transaction that was submitted to register the DID on the blockchain.
    * isstring = TX_HASH[0:19]
* checksum is a 4-byte string used to prevent human typing errors. 
    * checksum = SHA3-256(network-id | idstring)[0:3]

## 2. DID Document

### 2.1 DID Document Example
ICON DID Document includes following objects.
* publicKey : public key information that will be used to authenticate an entity.
* authentication : a mechanism that proves the DID owner’s possession of the matching private key that corresponds to the public key registered in the DID Document.

#### example
``` 
{
      "@context": "https://w3id.org/did/v1",
      "id": "did:icon:ARvG21FG1PskzWiaFhWAFg25IH",
      "created": "2018-10-01T12:00:00Z",
      "updated": "2018-11-01T10:00:00Z",
      "publicKey": [
        {
          "id": "did:icon:ARvG21FG1PskzWiaFhWAFg25IH#keys-1",
          "type": "Secp256k1VerificationKey2018",
          "owner": "did:icon:ARvG21FG1PskzWiaFhWAFg25IH",
          "publicKeyHex": "02b9bd353320b2a4585238965b3fafa9fa556776d3302428a0ab6fb796c6f2301b"
        }
       ],
      "authentication": [
        {
          "type": "Secp256k1Authentication2018",
          "publicKey":"did:icon:ARvG21FG1PskzWiaFhWAFg25IH#keys-1"
        }
      ],
}
```

### 2.2 CRUD Operations
ICON DID is managed by a dedicated Smart Contract on the blockchain. The owner of the ICON DID can manage the DID Document by sending a transaction to the ICON Smart Contract. 

#### 2.2.1 Create (Register)
Entity must submit a transaction to register a new DID Document to the ICON blockchain. The transaction must contain following elements. The requesting entity must prove its possession of the matching private key that corresponds to the public key in the document by providing a signature.  

``` 
“data” : {
               “method” : “create”
               “params” : {
                       “header” : {
                             “alg” : “auth_algorithm”
                       },
                       “payload” : {
                             “publicKey” : [{
                                   “type”: public key type,
                                   “publicKeyHex” : hex encoded public key
                             }],
                             “authentication” : [{
                                   “type” : authentication type,
                                   “publicKey” : publicKey object
                             ]}
                       },
                       “signature” : signature
               }
}
```

#### 2.2.2 Read (Resolve)
Anyone can look up the DID Document registered in the ICON blockchain. The DID Document query must contain following objects. 

```
“data” : {
               “method” : “get_did”,
               “params” : {
                     “id” : icon-did to query
               }
}
```

#### 2.2.3 Update (Replace)
To update the DID Document registered in the ICON blockchain, entity must send a transaction. DID document will be replaced by the new transaction. Only the DID owner is permitted to update, and the requesting entity must prove its identity using the authentication mechanism defined in the DID Document.  

```
“data” : {
               “method” : “update”
               “params” : {
                       “header” : {
                             “alg” : “auth_algorithm”
                       },
                       “payload” : {
                             “id” : icon-did to update
                             “publicKey” : [{
                                   “type”: public key type,
                                   “publicKeyHex” : hex encoded public key
                             }],
                             “publicKey” : [{
                                   “type”: public key type,
                                   “publicKeyHex” : hex encoded public key
                             }],
                             “authentication” : [{
                                   “type” : authentication type,
                                   “publicKey” : publicKey object
                             ]}
                       },
                       “signature” : signature
               }
}
```

#### 2.2.4 Delete (Revoke)
To delete (revoke) a registered DID, entity should send the following transaction. The requesting entity must prove its ownership of the DID using the authentication mechanism defined in the DID document.

```
“data” : {
               “method” : “delete”
               “params” : {
                       “header” : {
                             “alg” : “auth_algorithm”
                       },
                       “payload” : {
                             “id” : icon-did to delete
                       },
                       “signature” : signature
               }
}
```

## 3. Security Considerations
TODO

## 4. Privacy Considerations
TODO

## References
[1]. ICON foundation, https://icon.foundation

[2]. ICON developers portal, https://icondev.io

[3]. ICON project github, https://github.com/icon-project

[4]. W3C Decentralized Identifiers (DIDs) v0.11, https://w3c-ccg.github.io/did-spec
