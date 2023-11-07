# JWT Validation Process

## Server-Side Signature Creation:

### Header and Payload Construction:
- The server constructs a JSON object for the header and the payload.
- The header includes the type of token (`JWT`) and the signature algorithm (`RS256`).
- The payload contains claims about the authenticated user and other metadata.

### Base64Url Encoding:
- The header and payload are serialized to JSON and Base64Url encoded, creating two strings:
    - `BASE64URL_ENCODED_HEADER`
    - `BASE64URL_ENCODED_PAYLOAD`

### Signature Generation:
- The server concatenates the encoded header and payload with a period (`.`) separator.
- It computes the SHA-256 hash of this concatenated string.
- Using its private RSA key, the server signs the hash, creating the `Signature`.

### JWT Assembly:
- The complete JWT is assembled by concatenating the encoded header, payload, and signature, each part separated by periods.
- This JWT is sent to the client.

## Client-Side JWT Receipt:

### JWT Splitting:
- Upon receiving the JWT, the client splits the token into three components:
    - `Header`
    - `Payload`
    - `Signature`

### Header Inspection:
- The client decodes the Base64Url encoded header to extract the `alg` and `kid`.

## Public Key Retrieval:

### Fetching Public Key:
- The client requests the public key from the Identity Provider's (Azure AD) JWKS endpoint, using the `kid`.

### Key Delivery:
- Azure AD returns the public key in a JSON object.

## Client-Side Signature Verification:

### Hash Computation:
- The client concatenates the received `BASE64URL_ENCODED_HEADER` and `BASE64URL_ENCODED_PAYLOAD`.
- It computes the SHA-256 hash of this concatenated string.

### Signature Decryption:
- Using the public key, the client decrypts the `Signature`.
- The decryption result should match the original hash computed by the server.

### Signature Comparison:
- The client compares the decrypted hash with the computed hash.
- If the hashes match, the signature is valid, and the JWT is considered authentic.

### Validation Outcome:
- If the hashes match, the client validates the JWT and proceeds.
- If the hashes do not match, the JWT is rejected as it might have been altered.
