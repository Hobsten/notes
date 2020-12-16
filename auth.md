
Just a reminder on `encoding` and `decoding` before getting into it:  
`"hello" | urlencode == "hello"`  
`"hello" | base64encode == "aGVsbG8="`  
`"hello" | base64urlencode == "aGVsbG8="	// same as base64, except '+' '/' '=' is replaced with '-' '_' '%'`  
`"hello" | urlencode == "68656c6c6f"`  

## JWT (RFC 7519)

A good resource [jwt.io](https://jwt.io)

JSON Web Tokens allow you to digitally sign information (claims) with a signature that can be verified at a later time with a secret signing key. 
* Securely transfer information between any two entities. 
* Self-contained
	* Contains information about the user 
	* This information saves us a database query (*after the user is logged in*)

JWT is used for
* **Authentication**
* **Information exchange**

Three distinct parts of a JWT that is URL encoded
* **Header** - metadata for the token and at a minimal contains the type of the signature and/or encryption algorithm
* **Claims** - contains any information that you want signed (* authentication token*)
* **JSON Web Signature (JWS)** - the headers and claims digitally signed using the algorithm in the specified header  
The most important thing is, they are ***signed***. This ensures *the claims have not been tampered with* when stored and passed between your service or another service - *verifying the signature*. 

`aaaa.bbbb.cccc` where `aaaa` is the header `base64Url` encoded, `bbbb` is the claims `base64Url` encoded, and `cccc` is the HMAC of `aaaa` + `bbbb` + `secret` in `base64Url` encoded format. If you change `aaaa` or `bbbb`, naturally  when you HMAC(`aaaa`,`bbbb`,`secret`) it won't match `cccc`. Therefore you cannot tamper with the data without invalidating it.

This entire structure, `aaaa.bbbb.cccc`, is what we refer to as the ***JWT***. 

* Client Request
```
//header (base64urlencoded)
{
    "alg": "HS256", //denotes the algorithm (shorthand alg) used for the  signature is HMAC SHA-256
    "typ": "JWT" //denotes the type (shorthand typ) of token this is
}
 
//claims (base64urlencoded)
{
    "sub": "tom@stormpath.com",
    "name": "Tom Abbott",
    "role": "user"
}
//hmacsha256
hmac // HMAC(base64UrlEncode(header) + "." + base64UrlEncoded(payload), secret)
```
* Server side
```
// no database queries needed
HMAC(request.header + "." + request.payload, secret) == request.hmac
```




#### Alternative, JWT in code
```
var headers = base64URLencode(myHeaders);
var claims = base64URLencode(myClaims);
var payload = header + "." + claims;
 
var signature = base64URLencode(HMACSHA256(payload, secret));
 
var encodedJWT = payload + "." + signature;
```

* If signature algorithm is set to `none` and it should be signed, it should fail verification immediately. 
* Do not contains any sensitive data in JWT. Data in claims can easily be decoded and read. 
* If you are worried about replay attacks, include *nonce* (`jti` claim), *expiration time* (`exp` claim) and *creation time* (`iat` claim) in the claims. 

### JWT vs JWS vs JWE
* JWT - unsigned token (plaintext)
* JWS - signed token 

## Session token
