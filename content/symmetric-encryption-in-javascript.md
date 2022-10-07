---
title: "Symmetric Encryption in JavaScript"
date: 2019-04-17T11:52:54+01:00
categories: ["Software"]
tags: ["cryptography", "javascript", "nodejs"]
images: ["/encryption.jpeg"]
draft: false
---

{{< figure src="/encryption.jpeg" alt="Encryption_image" >}}

Symmetric-key algorithm is a cryptographic algorithm that uses the same cryptographic key for both encrypting of plaintext and decrypting of ciphertext. It means that two or more parties can share this same secret key to maintain a private data. This algorithm is pretty fast and suitable for encrypting large data. Symmetric key algorithm can use either block cipher or stream cipher. The symmetric-key algorithms includes AES(Rijndael), Serpent, Blowfish, DES, Twofish, RC4 and in this article I’ll be talking about AES(Rijndael).


#### Advanced Encryption Standard(AES)

In January 1997 NIST announced a call for creating a new cipher. Various ciphers were proposed by many groups and after examining them extensively based on speed and security parameters etc, NIST finally chose Rijnadael algorithm. On October 2000 it was selected as advanced encryption standard(AES) and the best algorithm in terms of security, cost, resilience, integrity and survillance of the algorithm.

Rijndael was developed by two Belgian cryptographers, Joan Daemen and Vincent Rijmen at the Electrical Engineering Department of Katholieke University in Leuven.


#### AES Encryption in JavaScript

Nodejs provides a crypto module for working with cryptographic functionalities like OpenSSL’s hash, cipher, decipher, sign, verify and HMAC functions. There are many cryptography libraries out there for browser javascript and nodejs as well but i’m gonna use the nodejs built-in crypto module on this one.

I believe a program is worth a thousand words 😀. So let’s write one.

```js
const crypto = require('crypto')

const algorithm = 'aes-192-cbc'
const password = 'Password used to generate key'
const key = crypto.scryptSync(password, 'salt', 24)
const iv = Buffer.alloc(16)
const cipher = crypto.createCipheriv(algorithm, key, iv)

cipher.on('readable', () => {
    console.log(cipher.read().toString('hex'));
})

cipher.write('some plain text data to encrypt');
cipher.end();
```

As you can see it takes just 13 lines of code. To create a cipher, nodejs crypto module makes use of createCipheriv() method which creates and returns a cipher object with the given algorithm, key and initialization vector(iv). In this piece of code, this cipher object is used as a readable stream and writable stream. It’s first parameter is the underlying algorithm being used which is based on OpenSSL. The next is the raw key used by the algorithm and the other is the [initialization vector](https://en.wikipedia.org/wiki/Initialization_vector).

```js
const crypto = require('crypto')

const algorithm = 'aes-192-cbc'
const password = 'Password used to generate key'
const key = crypto.scryptSync(password, 'salt', 24)
const iv = Buffer.alloc(16)
const decipher = crypto.createDecipheriv(algorithm, key, iv)

decipher.on('readable', () => {
    console.log(decipher.read().toString('utf8'));
})

decipher.write('b084a02f106834f4349f7b162bff92ac4c7449886446b2459e7768935ef94882', 'hex')
decipher.end();
```

This is just the reverse reaction of our first example. There’s nothing new in this one except the use of createDecipheriv() method to create a decipher class used for decrypting of data.

We’ll go a bit advanced on now. In the real world, while working on cryptographic schemes, it’s pretty common to store the encrypted data on the disk or where ever you find convenient. Let’s go a bit deeper to encrypt a file on the disk.

```js
const crypto = require('crypto')
const fs = require('fs')
const path = require('path')

const algorithm = 'aes-192-cbc'
const password = 'This is my password'
const key = crypto.scryptSync(password, 'salt', 24)
const iv = Buffer.alloc(16)
const cipher = crypto.createCipheriv(algorithm, key, iv)

// write the encrypted data to disk
const homeDir = process.env.USERPROFILE
const fileToEncrypt = path.join(homeDir, "file-name.txt")
const input = fs.createReadStream(fileToEncrypt)
const output = fs.createWriteStream(`${fileToEncrypt}.sec`);

input.pipe(cipher).pipe(output)
fs.unlinkSync(fileToEncrypt)
console.log('Encryption successfull!!!')
```

In this piece of code, the cipher object is used with piped steams to read a file, encrypt its contents and store it on the disk with a different file extension. When this is done, the original file remains on the disk along with the encrypted version of it. To make your encryption worth it, you need to get rid of the original version and nodejs provides the unlinkSync() method from the built-in file system module.

```js
const crypto = require('crypto')
const fs = require('fs')
const path = require('path')

const algorithm = 'aes-192-cbc'
const key = crypto.scryptSync(password, 'salt', 24)
const iv = Buffer.alloc(16)
const decipher = crypto.createDecipheriv(algorithm, key, iv)

const homeDir = process.env.USERPROFILE
const fileToEncrypt = path.join(homeDir, "file-name.txt")
const input = fs.createReadStream(fileToEncrypt);
const output = fs.createWriteStream(fileToEncrypt.slice(0, fileToEncrypt.length-4));

input.pipe(decipher).pipe(output)
fs.unlinkSync(fileToEncrypt)
console.log('Decryption successfull!!!')
```

The reverse reaction of the encryption is quiet clear and i’ll leave you to figure it out.

You can check out [secrecy](https://github.com/Spatocode/secrecy), a cli tool which uses AES algorithm for encrypting and keeping your files secret.

Thanks for reading. Kindly share.