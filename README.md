# Encrypting files with OpenSSL

## Theory

If there is a need to transmit files over an insecure medium, like the Internet, OpenSSL can help us to generate files, which are encrypted and thus very secure. In our case, we use OpenSSL to generate public/private keypairs so we are able to encrypt our binary data with those keys. The private key MUST be guarded from non authorized acces since this is the key we decrypt the data with. The public key, can be shared freely and is _**not**_ able **de**crypt the data - just to **en**crypt it.

You may check out the theory behind public/private key cryptography you may check out the following [Wiki](https://en.wikipedia.org/wiki/Public-key_cryptography)

If you don't want to read long explanations, here is how it works: _Person A_ wants to exchange data securely with _person B_ over an insecure medium ( e.g. Internet ). _Person B_ generates a keypair and shares his or her **public key** with _person A_. _Person A_ then encrypts the data with _person B_'s **public key** and sends it over the insecure medium to _person B_. _Person B_ is then able to decrypt the data with his **private key** which has _never_ left his or her computer.

## Usage


### Requisites

**Generate Certificate Pair**

Before we are able to en-/de-crypt our data, we need the keypairs to use. These keys can be generated with OpenSSL.

The command used is:
`openssl req -x509 -nodes -newkey rsa:2048 -keyout private-key.pem -out public-key.pem`

- `-x509` is the certificate type used
- `-nodes` means that the private keys should not be encrypted
- `-newkey` tells OpenSSL to generate new keys
- `rsa:2048` stads for the algorithm and the amount of bits used
- `-keyout` specifies the name of the private key
- `-out` specifies the name of the public key


### Encrypting files

**Encrypt File**

The command used is:
`openssl smime -encrypt -binary -aes-256-cbc -in ~/original_file.bin -out ./encrypted_file.bin -outform DER public-key.pem`

- `smime` this is normally used to encrypt mail messages
- `-encrypt` tells OpenSSL that we want to encrypt data
- `-binary` indicates that we are dealing with binary data (even if it isn't)
- `-aes-256-cbc` establishes the cypher algorithm to use
- `-in` specifies the file to encrypt
- `-out` specifies the filename of the encrypted data
- `-outform` configures the output format for the key
- `public-key.pem` is the public key ( the one that we can share ) we have generated in the previous step


### Decrypting files

**Decrypt File**

The command line is:
`openssl smime -decrypt -in ./encrypted_file.bin -binary -inform DEM -inkey private-key.pem -out ~/original_file.bin`

- `smime` again tells OpenSSL that we are working with smime data
- `-decrypt` starts the decryption process
- `-in` specifies the encrypted file
- `-binary` means that we are working with binary data here (as before)
- `-inform` configures the input key format
- `-inkey` points to the private key ( this is the one we want to keep private at all cost )
- `-out` is the filename of the decrypted data


### Script

These commands are available in form of a script. When generated through the script, the public/private keypair is stored under `~/.myKeys`.

When using the `encrypt-cert` or `decrypt-cert` scripts, the keys stored in the afore mentioned directory are used. If you'd like to specify them manually, don't use the script...

### Notes

It is important to note that this only works if the data can be fit entirely in RAM. So even though we don't have a theoretical limitation in size, we DO have a limitation in practice, depending on the available RAM.
