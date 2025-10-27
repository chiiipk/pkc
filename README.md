# AES in CBC and CTR — Decryptor
## How to build:
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install pycryptodome

## How to run:
python aes_decrypt.py

## Libraries:
PyCryptodome → AES block primitive (AES.MODE_ECB)
binascii → Hex decoding (unhexlify)

## How the decryption works (logic/steps):
CBC Mode:
1. Extract IV (first 16 bytes of ciphertext).
2. Split the rest into 16-byte blocks.
3. For each block:
X = AES_DEC_k(Ci) using AES-ECB.
$Pi = X \oplus C_{i-1}$ (with $C_{0}$ = IV).
4. Concatenate all Pi and remove padding.

CTR Mode:

1. The first 16 bytes of ciphertext are used as IV = nonce (8B) + initial counter (8B).
2. For each 16-byte ciphertext block:
Generate keystream = AES_ENC_k(nonce || counter_i)
XOR ciphertext with keystream: $Pi = C_{i} \oplus keystream_{i}$
Increment counter: counter_(i+1) = counter_i + 1
3. Join all plaintext blocks.

Function summary:
1. unpad: Removes padding from a decrypted plaintext block.
2. xor: Performs bitwise XOR between two equal-length byte strings.
3. decrypt_aes_cbc: Implements AES-CBC decryption manually using AES in ECB mode:
   Decode key and ciphertext from hex -> Split IV and ciphertext blocks -> For each ciphertext block: decrypt with AES-ECB and XOR with previous ciphertext block (or IV). -> Concatenate results and remove padding -> Returns the final plaintext (UTF-8 decoded)
4. decrypt_aes_ctr: Implements AES-CTR decryption manually using AES-ECB: Decode key and ciphertext from hex -> Split IV into nonce (8B) and counter (8B) -> For each 16-byte ciphertext block: Generate keystream, XOR keystream with ciphertext, Increment counter by 1 -> Join all plaintext bytes and decode to UTF-8.

## Plaintext:
1. Basic CBC mode encryption needs padding.
2. Our implementation uses rand. IV
3. CTR mode lets you build a stream cipher from a block cipher.
4. Always avoid the two time pad!
