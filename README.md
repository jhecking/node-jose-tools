## node-jose tools for JOSE key management, token signing and encryption

Swiss-army knife tool for [node-jose](https://github.com/cisco/node-jose).

It provides the ```jose``` command to
[JSON web-key (JWK)](https://tools.ietf.org/html/rfc7517) key and keystore
management as well as to handling
[JSON web-tokens (JWT)](https://tools.ietf.org/html/rfc7519) in, both, the
signed and encrypted variants.

## Contents

- [Installing](#installing)
- [Running](#running)
- [Tools](#tools)
  - [addkey](#addkey) - add a new key to a jwks
  - [newkey](#newkey) - create a new key (that can be used by addkey)
  - [listkeys](#listkeys) - list the key ids for all keys in a jwks
  - [findkey](#findkey) - find a kid in a jwks
  - [rmkey](#rmkey) - remove a kid from a jwks
  - [info](#info) - return basic information about a JWT without processing it
  - [sign](#sign) - creates and signs a JWS for a given payload
  - [verify](#verify) - verifies a JWS and return the payload
  - [encrypt](#encrypt) - encrypt a payload into a JWE
  - [decrypt](#decrypt) - decrypts a JWE and returns the payload
- [Examples](#examples)
  - [Creating new keys](#creating-new-keys)
  - [Adding a key to a keystore](#adding-a-key-to-a-keystore)
  - [Add a remote keystore locally](#add-a-remote-keystore-locally)
  - [Merging two keystores](#merging-two-keystores)
  - [List the key ids in a keystore](#list-the-key-ids-for-all-keys-in-a-keystore)
  - [Remove a key from a keystore](#remove-keys-from-a-keystore)
  - [Create a new key and add it to a keystore](#create-a-new-key-and-add-it-to-a-keystore)
  - [Create multiple keys](#create-multiple-keys)
  - [Find a key for a key id](#find-the-key-for-a-given-keyid)
  - [Create a JWS token](#create-a-jws-token)
  - [Verify a JWS token](#verify-a-jws)
  - [Encrypt a payload using RSA-OAEP and AES126GCM](#encrypt-a-payload-using-rsa-oaep-and-aes126gcm)
  - [Encrypt a string using the dir algorithm](#encrypt-a-string-using-the-dir-algorithm)
  - [Decrypt a JWE for you](#decrypt-a-jwe-for-you)
  - [Create a wrapped JWT using RSA-OAEP and AES126GCM](#create-a-wrapped-jwt-using-rsa-oaep-and-aes126gcm)
  - [Unwrap a JWE and verify an included JWS](#unwrap-a-jwe-and-verify-an-included-jws)

## Installing

On the command line run ```npm install -g node-jose-tools```.

## Running

```
> jose TOOL [OPTIONS]
```

The following tools are supported:

 - [addkey](#addkey) - add a new key to a jwks
 - [newkey](#newkey) - create a new key (that can be used by addkey)
 - [listkeys](#listkeys) - list the key ids for all keys in a jwks
 - [findkey](#findkey) - find a kid in a jwks
 - [rmkey](#rmkey) - remove a kid from a jwks
 - [info](#info) - return basic information about a JWT without processing it
 - [sign](#sign) - creates and signs a JWS for a given payload
 - [verify](#verify) - verifies a JWS and return the payload
 - [encrypt](#encrypt) - encrypt a payload into a JWE
 - [decrypt](#decrypt) - decrypts a JWE and returns the payload
 - [digest](#digest) - computes a SHA-2 digest of the provided input

This package installs the jose script into your system.


## Tools

### addkey

adds a key to a keystore and returns the JWK

SYNOPSIS:

```
> jose addkey [-q -U -C -b --update --quiet --create --beauty] [-j KEYSTORE] [KEYFILE ...]
```

Use this tool for adding keys to a keystore. If no keyfile is provided,
then ```addkey``` reads the key from STDIN.

The tool handles JWK, JWKS, PEM, DER, PKCS#8, PKIX, SPKI, and X509 formats.
If another keystore is presented for import, all keys are imported.

The ```addkey``` tool tries to import all provided keyfiles.

The following options and flags are supported:

 * ```-C, --create``` - creates a new keystore if the keystore file is not present.

 * ```-j, --jwks, --keystore KEYSTORE``` - loads the given keystore.

 * ```-q, --quiet``` - generates no output. This flag omits the output of the keystore.

 * ```-U, --update``` - updates the provided keystore.

 * ```-b, --beauty``` - pretty print JSON

### newkey

creates a new key according to the secifiations.

SYNOPSIS:

```
> jose newkey -s KEYSIZE [-t KEYTYPE] [-u USAGE] [-j KEYSTORE] [FLAGS]
```

The ```newkey``` allows to create one key or key-pair. It always returns the private key,
so one can add it to a keystore.

By default ```newkey``` returns the newly created private key in JWK format.

The following parameters are supported:

 * ```-s, --size KEYSIZE``` - creates a key for the given keysize or curve, if ```EC``` or ```OKP``` keys are requested.

 * ```-t, --type KEYTYPE``` - creates a new key of the provided key type. Only ```RSA```, ```oct```, ```EC```, and ```OKP``` are supported values.

 * ```-j, --jwks, --keystore KEYSTORE``` - loads the given keystore. If this parameter is missing ```newkey``` tries to load a keystore from ```STDIN```. If that fails, then a new keystore is created.

 * ```-K, --as-keystore``` - return the new key in JWKS format.

 * ```-q, --quiet``` - generates no output. This flag omits the output of the keystore.

 * ```-U, --update``` - updates the provided keystore if -j, --jwks, or --keystore is present.

 * ```-r, --RSA, --rsa``` - alternative for --type rsa

 * ```-e, --EC, --ec``` - alternative for --type ec

 * ```-o, --oct, --OCT``` - alternative for --type oct

 * ```-d, --dh, --OKP, --okp``` - alternative for --type okp (RFC8037 D&H keypairs)

 * ```-b, --beauty``` - pretty print JSON

 Note that ```oct``` require a minimum keysize of 256 bit and RSA-keys require a
 minimum keysize of 2048 bit.

### listkeys

lists all keyids in a given keystore.

SYNOPSIS

```
> jose listkeys -j KEYSTORE
```

The tool accepts the ```-j, --jwks, and --keystore``` parameters. If no keystore is
provided, then ```listkeys``` will try to read the keystore from ```STDIN```.

### findkey

finds a key id in a keystore and returns the JWK

SYNOPSIS:

```
> jose findkey [-q -r -k -p -b --cnfkey --cnfref --public --quiet --beauty] [-j KEYSTORE] KEYID
```

The tool ```findkey``` finds and returns a key from a keystore. If the key is present, then
the JWK is returned in the requested format. If the keyid is not found, then
findkey returns an error.

By default ```findkey``` returns the key as it is stored in the keystore.

The following options are accepted for manipulating the output.

 * ```-j, --jwks, --keystore KEYSTORE``` - loads the given keystore. If no keystore is provided, the keystore is loaded from ```STDIN```.

 * ```-c, --cnfkey``` - returns a RFC7800 confirmation key. This will include the key of octet keys. For all other keys the public key is returned. This will throw an error, if the keyid does not refer to a private key.

 * ```-k, --kid KEYID``` - OPTIONAL the key id to return. This option is for compliance with other tools.

 * ```-p, --public``` - returns the public key instead of a private key. If the key is a public key, this option does nothing.

 * ```-q, --quiet``` - generates no output. This flag is ideal to verify the presence of a key without obtaining it.

 * ```-r, --cnfref``` - returns a RFC7800 confirmation key reference. This will throw an error, if the keyid does not refer to a private key.

 * ```-b, --beauty``` - pretty print JSON

If no keystore is provided, findkey will load the the keystore from ```STDIN```,
this allows to pipe directly from the ```addkey``` tool.

### rmkey

removes a key with the provided keyid from the provided keystore and returns it

SYNOPSIS

```
> jose rmkey KEYID [-j KEYSTORE] [-U --update -q --quiet -b --beauty]
```

The tool ```rmkey``` allows to remove multiple keys in one got. In this case each key id must
be provided in a separate line of the ```KEYID```-string. If no ```KEYID``` is provided, ```rmkey``` tries to load the ```KEYID``` from the command line. Alternatively, ONE keyid can be passed using the ```-k``` or ```--kid``` option.   

 * ```-j, --jwks, --keystore KEYSTORE``` - loads the given keystore. If this parameter is missing newkey tries to load a keystore from ```STDIN```. If that fails, then ```rmkey``` fails with an error

 * ```-k, --kid KEYID``` - the key id in the keystore to be used in this operation.

 * ```-q, --quiet``` - generates no output. This flag omits the output of the keystore.

 * ```-U, --update``` - updates the provided keystore if ```-j, --jwks```, or ```--keystore``` is present.

 * ```-b, --beauty``` - pretty print JSON

By default ```rmkey``` returns the updated keystore on ```STDOUT```. If ```-j, --jwks``` or ```--keystore``` is provided, then the ```-U``` flag will overwrite the provided keystore.

### info

prints basic information for compressed JWT without processing it.

SYNOPSIS:

```
> jose info [-b] [JWTFILE]
```

If no ```JWTFILE``` is provided ```info``` will read the JWT from STDIN.

Add the ```-b``` flag to pretty print the result.

This tool provides quick information about a JWT, such as token type
and header claims. For JWS ```info``` also includes the payload claims.

Note that ```info``` does not attempt to validate or decrypt a token.

### sign

creates a signed JWS token.

SYNOPSIS:

```
> jose sign -j KEYSTORE -l ALG -k KEYID -i ISSUER -a AUDIENCE [FLAGS] [-p [PAYLOADFILE]]
```

One can pass a payload via ```STDIN``` to the ```sign``` tool (e.g., the output of findkey).

By default, ```sign``` will return the JWS in compact format.

The following parameters are accepted.

 * ```-a, --aud AUDIENCE``` - the target of the JWS that will verify the token

 * ```-F, --flat, --flattened``` - return the JWS as flattened JSON.

 * ```-G, --general, --json, --JSON``` - return the JWS in the full JSON format.

 * ```-i, --issuer ISSUER``` - the issuer of the JWS (probably used by the audience)

 * ```-j, --jwks, --keystore KEYSTORE``` - the keystore that contains the singing keys.

 * ```-k, --kid KEYID``` - the key id in the keystore to be used in this operation. This MUST be a privat key.

 * ```-l, --alg JWA``` - the signing algorithm (e.g., ```HS256```).

 * ```-N, --no-reference``` - indicates that the header must not contain a reference to the key.

 * ```-p, --payload``` - indicates to load a payload file. If no payload filename has been passed, then ```sign``` will load the payload from ```STDIN```.

 * ```-x, --exp TIMEOUT``` - add a validity timeout in seconds from now.

 * ```-b, --beauty``` - pretty print JSON

### verify

verifies a signed JWS token and returns the payload

SYNOPSIS:

```
> jose verify -j KEYSTORE -i ISSUER -a AUDIENCE
```

One can pass a payload via ```STDIN``` to the ```sign``` tool (e.g., the output of findkey).

The following parameters are accepted.

 * ```-a, --aud AUDIENCE``` - the target of the JWS that will verify the token

 * ```-i, --issuer ISSUER``` - the issuer of the JWS

 * ```-j, --jwks, --keystore KEYSTORE``` - the keystore that contains the singing keys.

### encrypt

creates a JWE token.

SYNOPSIS:

```
> jose encrypt -j KEYSTORE -l ALG -e ENC -k KEYID [-p [PAYLOADFILE]]
```

One can pass a payload via ```STDIN``` to the ```encrypt``` tool (e.g., the output of findkey).

The following parameters are accepted.

 * ```-j, --jwks, --keystore KEYSTORE``` - the keystore that contains the singing keys.

 * ```-k, --kid KEYID``` - the id of the key that should be used for encryption. This MUST be a public key.

 * ```-l, --alg JWA_KEY_ENCCRYPT``` - the key encryption algorithm as specified for JWA

 * ```-e, --enc JWA_CONTENT_ENCRYPT``` - The content encryption algorithm as specified for JWA.

 * ```-F, --flat, --flattened``` - return the JWS as flattened JSON.

 * ```-G, --general, --json, --JSON``` - return the JWS in the full JSON format.

 * ```-a, --aud AUDIENCE``` - OPTIONAL the audience of the token. ```encrypt``` tries to determine the audience from the payload (e.g. if a JWS is passed). If no audience is given and ```encrypt``` cannot determine the aud automatically , then the tools ends with an error.

  * ```-p, --payload``` - indicate that the last argument is a filename and not the actual payload. If this flag is used and no additional argument is provided the, then the payload is read from ```STDIN```.

### decrypt

decrypts a JWE token and returns the payload.

SYNOPSIS:

```
> jose decrypt -j KEYSTORE [PAYLOADFILE]
```

One can pass a payload via ```STDIN``` to ```decrypt``` (e.g., the output of ```encrypt```).

The following parameters are accepted.

 * ```-j, --jwks, --keystore KEYSTORE``` - the keystore that contains the private keys for decryption.

### digest

computes a SHA-2 digest (aka hash or checksum) of the provided input.

SYNOPSIS:

```
> jose digest [-s SIZE] [DATA]
```

The SHA-digest is returned as a base64url-encoded string.

The tool accepts the following sizes

- 256
- 384
- 512

as well as the corresponding SHA-labels.

## Examples

### Creating new keys

```
> jose newkey -t RSA -s 2048
> jose newkey -t oct -s 256
```

### Adding a key to a keystore

```
> jose addkey -j mykeystore.jwks privatekey.PEM
```

This will return the extended keystore to STDOUT.

For updating the keystore use the following variant:

```
> jose addkey -U -j mykeystore.jwks privatekey.PEM
```

### Add a remote keystore locally

This is useful to cache public keys from a server.

```
> jose addkey -j example.jwks https://your.server.host/certs
```

This will return the extended local keystore.

Use the ```-U``` flag to update the local keystore.

It is possible to read any supported key-format from a URL.

### Merging two keystores

```
> jose addkey -j example.jwks example-priv.jwks
```

Note: if the keystores have the same keyids, then this will result in duplicate keyids in the keystore file.    

Again, use the -U to store the extended keystore into mykeystore.jwks

### List the key ids for all keys in a keystore

```
> jose listkeys example.jwks
```

This will print one key ID per line, so the result will be:

```
foobar
barfoo
foorsa
```

### Remove keys from a keystore

```
> jose rmkey -b -j example.jwks foorsa
```

This will output a JWKS without the key "foorsa".

```
{
    "keys": [
        {
            "kty":"oct",
            "kid":"foobar",
            "k":"QYPTbIwxRbVuCLU0T3lQWYGP05asffZLAuM1KiNyqj4"
        },
        {
            "kty":"oct",
            "kid":"barfoo",
            "k":"-E_-rcOr6iesQ_BKO21DAuKdblhUmwciIx8Q6gUcUuG42Fw0zdPHPQtfZh19upvrh1Epevwz3Yc2a3YMGCJh1w"
        }
    ]
}
```

If you like to have less spaces in the output, remove the ```-b``` flag.

For updating the keystore use the -U or --update flag.

```
> jose rmkey -U -j example.jwks foorsa
```

### Create a new key and add it to a keystore

```
> jose newkey -t oct -s 256 | jose addkey -U -j mykeystore.jwks
```

Alternatively, one can write this command:

```
> jose newkey -t oct -s 256 -U -j mykeystore.jwks
```

Tipp: the -q flag silences the command and it will not prompt any other output
than error messages

### Create multiple keys

The -K flag tells the newkey command to return the keystore instead of an
individual key.

```
> jose newkey -t oct -s 256 -K |  jose newkey -t ec -s 256 -K |  jose newkey -t rsa -s 2048 -K | jose addkey -U -j mykeystore.jwks
```

### Find the key for a given keyid

```
> jose findkey -j example.jwks foobar
```

This will return the private key for the key ```foobar```, if present.

To export the public key, use

```
> jose findkey -p -j example-priv.jwks foorsa
```

This results in the following output (linebreaks are inserted for readability):

```
{
    "kty":"RSA",
    "kid":"foorsa",
    "e":"AQAB",
    "n":"sFhX2R0ColcUrlU224bzhvCOwngQGGc23BT4btYBtMlM9kEnC_rHpbI45P4LGqGZO-vy8
    PK9d9DPtvkdwsc1gxMOe__HoxwSG8aaapEd4NXgMKKXviAJUJbkY7pb9NHvImm6_1ESm6FRT4a
    5LdRp5kAJdbfuwkfNRQxzWf-p3wYZoUMxcz3fAdWME55Z7y_YMTIMAI3hbRSw50eaNoY4gggGK
    Huz42PrDeclxtQJFI_-nzm7jzEvs_JFIZ0yyTePi4nTOLWNzSFcc43gcfHHOK5okXuiAmZyu-3
    voH3rnU85Xb2lkZrQd4Rjxhf6YNYzTzCsmh6Aa2gAloHBqfJU9Q"
}
```

To wrap the key into RFC7800 key confirmation use:

```
> jose findkey -c -j example.jwks foobar
```

This results in (linebreaks are inserted for readability):

```
{
    "cnf":{
        "jwk":{
            "kty":"oct",
            "kid":"foobar",
            "k":"QYPTbIwxRbVuCLU0T3lQWYGP05asffZLAuM1KiNyqj4"
        }
    }
}
```

To pass a key reference as a RFC7800 key confirmation use:

```
> jose findkey -r -j example.jwks foobar
```

This results in (linebreaks are inserted for readability):

```
{
    "cnf":{
        "kid":"foobar"
    }
}
```

### Create a JWS token

```
> jose sign -j example.jwks -a audience -i clientid -l HS256 -k foobar
```

This results in the following compact JWS (linebreaks are inserted for readability):

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6ImZvb2JhciJ9.eyJleHRyYSI6e30sImlz
cyI6ImNsaWVudGlkIiwiYXVkIjoiYXVkaWVuY2UiLCJzdWIiOiJjbGllbnRpZCIsImlhdCI6MTUxN
DAyMTg5MH0.3n7UkVtMA12ZuQ7fjf0h6FsmPqusTBOrs7N7zNMTcfg
```

Add some extra payload via ```STDIN```.

```
> echo '{"payload": "mypayload"}' | jose sign -j example.jwks -a audience -i clientid -l HS256 -k foobar -p
```

This will result in:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6ImZvb2JhciJ9.eyJwYXlsb2FkIjoibXlw
YXlsb2FkIiwiaXNzIjoiY2xpZW50aWQiLCJhdWQiOiJhdWRpZW5jZSIsInN1YiI6ImNsaWVudGlkI
iwiaWF0IjoxNTE0MDIyMTA0fQ.gRSQAU8HcoVnkAcXeeBPGyaFI2qdAD5wsR31AbpkBYc
```

Use the ```-F``` flag to request the flattened JWS format:

```
> jose sign -j example.jwks -a audience -i clientid -l HS256 -k foobar -F
```

will result in:

```
{
    "payload":"eyJleHRyYSI6e30sImlzcyI6ImNsaWVudGlkIiwiYXVkIjoiYXVkaWVuY2UiLCJzdWIiOiJjbGllbnRpZCIsImlhdCI6MTUxNDAyMjIxNX0",
    "protected":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6ImZvb2JhciJ9",
    "signature":"XfsZELWK84ZPiANAls8GbeYk7dg06HDDE94YhPT57EY"
}
```

Use the ```-G``` flag to load get the general JWS format.

### Verify a JWS

```
> jose verify -j example.jwks  -a audience -i issuer token.jwt
```

You can also pass the token via stdin:

```
> echo TOKENSTRING | jose verify -j example.jwks -a audience -i issuer
```

### Encrypt a payload using RSA-OAEP and AES126GCM

```
> echo PAYLOADSTRING | jose encrypt -j example.jwks -k foorsa -l RSA-OAEP -e A126GCM -p
```

### Encrypt a string using the dir algorithm

```
> jose encrypt -j example.jwks -k foobar -l dir "hello world"
```

### Decrypt a JWE for you

```
> echo JWETOKENSTRING | jose decrypt -j example-priv.jwks
```

### Create a wrapped JWT (using RSA-OAEP and AES126GCM)

Note that you are free to use any of the other alg/enc combinations if you have the appropriate keys.

```
> jose sign -j example.jwks -a audience -i myid -l HS256 | jose encrypt -j example.jwks -k foorsa -l RSA-OAEP -e A126GCM -p
```

Create a confirmation key for a targeted audience in a wrapped JWT:

```
> jose findkey -k -j example.jwks barfoo | jose sign -j example.jwks -k foobar -a audience -i myid -l HS256 | jose encrypt -j example.jwks -k foorsa -l RSA-OAEP -e A126GCM -p
```

## Unwrap a JWE and verify an included JWS

```
> echo TOKENSTRING | jose decrypt -j example-priv.jwks | jose verify -j example.jwks -a audience -i issuer
```
