---
layout: post
title:  "JWT Attacks"
author: erik
categories: [ Web ]
image: https://user-images.githubusercontent.com/47476901/176045278-117c9d1b-7b56-49ec-9764-e87c03d9b000.png
beforetoc: ""
toc: false
tags: [ Web Security, Payloads ]
---

- JSON Web Tokens (JWT) are a format for sending cryptographically signed JSON data between systems.
- In theory, they can contain any type of data, but are most commonly used for authentication, session management and access control mechanisms.
- Unlike classic session tokens, all the data needed by a server is stored client-side within the JWT itself. This makes JWTs a popular choice for highly distributed websites where users need to interact seamlessly with multiple back-end servers. 

# Table of Contents
1. [JWT Signature](#jwtsign)
2. [JWT Headers](#jwtheaders)<br>
  2.1 [JWK](#jwk)<br>
  2.2 [JKU](#jku)<br>
  2.3 [KDI](#kdi)
3. [Unverified signature](#Unverifiedsignature)
4. [Faulty signature verification](#Faultysignatureverification)
5. [Weak signature key](#Weaksignaturekey)
6. [Jwk header injection](#Jwkheaderinjection)

---

#### JWT Signature <a name="jwtsign"></a>
The server issuing the token usually generates the signature by hashing the header and payload. In some cases, they also encrypt the resulting hash. In any case, this process involves a secret key. Without knowing this secret, it is not possible to generate a valid signature for a given header and payload. This provides a mechanism for servers to verify that none of the data has been tampered with since the token was issued, since any change to the header or payload would mean that the signature no longer matches.  
Knowing the key that generates the signature we will be able to generate tokens to our liking, this would be a problem because we can create a token for example as an administrator. 

A JWT consists of 3 parts:
  - Header
  - Payload
  - Signature
  
Each of them is separated by a dot, as shown in the following example: 

![JWTexample](https://user-images.githubusercontent.com/47476901/176049678-09b365dd-72bc-449a-b3b8-1b5952cd3aa6.png)

#### Headers JWT <a name="jwtheaders"></a>

**JWK** <a name="jwk"></a>

The JSON Web Signature Specification (JWS) describes an optional `jwk` header parameter, which servers can use to embed their public key directly inside the token in JWK format.

Example of the JWK header:
```json
{
   "kid":"ed2Nf8sb-sD6ng0-scs5390g-fFD8sfxG",
   "typ":"JWT",
   "alg":"RS256",
   "jwk":{
      "kty":"RSA",
      "e":"AQAB",
      "kid":"ed2Nf8sb-sD6ng0-scs5390g-fFD8sfxG",
      "n":"yy1wpYmffgXBxhAUJzHHocCuJolwDqql75ZWuCQ_cb33K2vh9m"
   }
```

**JKU** <a name="jku"></a>

Instead of incorporating public keys directly using the `jwk` header parameter, some servers allow you to use the `jku` header parameter (JWK Set URL) to reference a JWK Set containing the key. When verifying the signature, the server obtains the relevant key from this URL.

```json
{
    "keys": [
        {
            "kty": "RSA",
            "e": "AQAB",
            "kid": "75d0ef47-af89-47a9-9061-7c02a610d5ab",
            "n": "o-yy1wpYmffgXBxhAUJzHHocCuJolwDqql75ZWuCQ_cb33K2vh9mk6GPM9gNN4Y_qTVX67WhsN3JvaFYw-fhvsWQ"
        },
        {
            "kty": "RSA",
            "e": "AQAB",
            "kid": "d8fDFo-fS9-faS14a9-ASf99sa-7c1Ad5abA",
            "n": "fc3f-yy1wpYmffgXBxhAUJzHql79gNNQ_cb33HocCuJolwDqmk6GPM4Y_qTVX67WhsN3JvaFYw-dfg6DH-asAScw"
        }
    ]
}
```

**KDI** <a name="kdi"></a>

The header of a JWT can contain a `kid` parameter (key ID), which helps the server identify which key to use when verifying the signature.

Example of the KDI header:
```json
{
    "kid": "7a0faa13-f336-49ff-9a3b-3cda86ed3eab",
    "alg": "RS256"
}
```
---

**Bypass of JWT authentication through unverified signature** <a name="Unverifiedsignature"></a>

The session cookie is a JWT token.
![cookie-LAB1](https://user-images.githubusercontent.com/47476901/176046787-d0bd846a-a2ee-44fd-90a9-07265dfc33ab.png)


With a burpsuite extension we can easily decode the tokens.
We can see that the token contains which user it refers to in the session.
![JWTdecoded-LAB1](https://user-images.githubusercontent.com/47476901/176046811-b42723b6-3e90-4c4d-bc5e-125f9a2332ea.png)

```json
{
  "kid": "55b9956e-6cf5-4010-9423-eb7a9746a5d7",
  "alg": "RS256"
},
{
  "iss": "portswigger",
  "sub": "wiener",
  "exp": 1655146551
}
```

If the web application has not properly configured the token system, it is possible that it does not validate the signature that generates them, thus causing that we can modify the content of the token to our liking making useless the JWT token system.

Taking advantage of this configuration failure, we change the user to administrator

![jwtdecodedadmin-LAB1](https://user-images.githubusercontent.com/47476901/176046830-94e4ab2d-69c5-48ef-a855-4a6c78d23b75.png)

```json
{
  "kid": "55b9956e-6cf5-4010-9423-eb7a9746a5d7",
  "alg": "RS256"
},
{
  "iss": "portswigger",
  "sub": "administrator",
  "exp": 1655146551
}
```

We apply the token to the session and reload the account panel.

![adminprofile-LAB1](https://user-images.githubusercontent.com/47476901/176046859-19aa9cb6-97d0-420b-b963-129923c5b12c.png)

---

**Bypass of JWT authentication through verification of faulty signature** <a name="Faultysignatureverification"></a>

The token system has a bug in the signature configuration that allows modifying the header so we can change the algorithm by which the signature is built and create again a token to our liking.

JWT unmodified:

![JWTnormal-LAB2](https://user-images.githubusercontent.com/47476901/176046872-5aedd549-7bd1-4218-ba44-ad21c48e870e.png)

Matches our account: Wiener

![normalaccount-LAB2](https://user-images.githubusercontent.com/47476901/176046904-40d10c3b-9dec-4bc3-be7f-e4819fa711ae.png)

**`We modified the HEADER`** of the algorithm to `none` to make the signature completely useless and thus change the user name to `administrator`.

The token **`does not contain the signature part`** now, since the algorithm value we have modified is now null.

![admintoken-LAB2](https://user-images.githubusercontent.com/47476901/176046916-b1f00c52-0258-4206-9dc2-42caa180b456.png)


Account referenced by the token name modified above due to the verification failure.

![adminpanel-LAB2](https://user-images.githubusercontent.com/47476901/176046936-53a5c661-0f55-4f2e-9856-2c2a1cc3f2b0.png)

---
**Bypass of JWT authentication using a weak signature key**. <a name="Weaksignaturekey"></a>

When the key used to generate the signature is weak, we can do a dictionary attack to try to guess it and thus generate our own valid tokens using a valid signature because we have the key.

We can see that JWT tokens are used in user sessions.

![normaluser-LAB3](https://user-images.githubusercontent.com/47476901/176046984-d12f7afa-dba9-4c53-9c7a-029a44a694dd.png)

Without the key that generates the signature we cannot create new tokens. We proceed to crack the signature by dictionary attack.

John The reapper cracking the token. 
We write the token in a file and crack it with john or hashcat.

![john-LAB3](https://user-images.githubusercontent.com/47476901/176046997-31528b45-50a4-4c8f-a601-f158e1fefdad.png)

The key that generates the token signature is `secret1`.

We proceed to generate the token for an administrator session.

When we indicate that the key is `secret1` it indicates that the signature is valid, which indicates that the token that we have created as administrator user is valid in the application.

![keysignature-LAB3](https://user-images.githubusercontent.com/47476901/176047010-857f368e-e6d1-4585-aa42-7a96699dd465.png)


We apply it with cookie and it authenticates us with the account to which the token belongs, in this case we indicate the administrator user.

![admin-LAB3](https://user-images.githubusercontent.com/47476901/176048400-42fa6b8c-b09c-4617-907a-f7c67b368855.png)

---

**Bypass of JWT authentication through jwk header injection** <a name="Jwkheaderinjection"></a>

The JSON Web Signature (JWS) specification describes an optional `jwk` header parameter, which servers can use to embed their public key directly inside the token in JWK format.

This is our user's token.

![tokenusernormal-LAB4](https://user-images.githubusercontent.com/47476901/176047117-442cf96c-5ad2-4c84-b4da-4bdbbbf1ca53.png)

We can inject in the header a key generated by us, since the configuration of how token headers are interpreted is misconfigured and allows injecting headers.

> Ideally, servers should only use a limited whitelist of public keys to verify JWT signatures. However, misconfigured servers sometimes use whatever key is embedded in the `jwk`parameter.

Thanks to this we can include in the header the jwk parameter which is used to embed the public key directly, this allows us to have control over the rest of the token since now the public key is the one we have control over.

We proceed to embed the header to modify the token to a privileged user.
And generate the key thanks to the `JWT Editor Keys` extension of burpsuite.

![generatekey-LAB4](https://user-images.githubusercontent.com/47476901/176047127-b8ac64ff-aea1-4cba-a59d-c8c929c1c10a.png)


Once we have the key we apply it in the token in the header part.
We apply the JWK header and also change the user parameter to refer to the administrator user.

Click on the attack button and `embedded JWK` to create the signature with the key we have created. 

![attackapplykey-LAB4](https://user-images.githubusercontent.com/47476901/176047143-78bae47d-7d8d-4a47-9049-d2a342e25f15.png)

![option-LAB4](https://user-images.githubusercontent.com/47476901/176047157-29e27610-f6fe-4bff-8ca3-2d0509be0885.png)


The token structure would look like this:

![tokenfinal-LAB4](https://user-images.githubusercontent.com/47476901/176047237-35e26f4a-92fa-46cd-a4b1-70f7218ab4f0.png)

If we forward the request with the new token we see that it has been validated and now belongs to the administrator user.

![adminpannel-LAB4](https://user-images.githubusercontent.com/47476901/176047244-ee0e58db-5c92-4aa4-8925-28b42f01cd29.png)
