This will be Introduction module to Cryptography and basic Algorithms used on it, it will be in a little detailed as needed.
# Table of Contents

1. Substitution Cipher
2. Transposition Cipher
3. Hashing
4. HMAC
5. Diffie-Hellman Key Exchange
6. PKI and SSL/TLS
7. Authenticating with Passwords
8. Examples on Cryptography and Data
# **Substitution Cipher**

![[Pasted image 20241218120703.png]]

Caesar cipher is considered a **substitution cipher** because each letter in the alphabet is substituted with another.

![[Pasted image 20241218112631.png]]

Encryption

```python
# Encryption
text = "Hello, World!"
shift = 3
encrypted = ""

for char in text:
    if char.isalpha():  # Check if the character is a letter
        shift_base = 'A' if char.isupper() else 'a'
        encrypted += chr((ord(char) - ord(shift_base) + shift) % 26 + ord(shift_base))
    else:
        encrypted += char  # Non-alphabetic characters remain unchanged

print("Encrypted:", encrypted)
```

Decryption

```python
decrypted = ""
for char in encrypted:
    if char.isalpha():  # Check if the character is a letter
        shift_base = 'A' if char.isupper() else 'a'
        decrypted += chr((ord(char) - ord(shift_base) - shift) % 26 + ord(shift_base))
    else:
        decrypted += char  # Non-alphabetic characters remain unchanged

print("Decrypted:", decrypted)
```

# **Transposition Cipher**

Another type of cipher is called **transposition cipher**, which encrypts the message by changing the order of the letters. Let’s consider a simple transposition cipher in the figure below. 

We start with the message, “THIS IS ALPHA BRAVO CONTACTING TANGO HOTEL MIKE”, and the key `42351`. 
After we write the letters of our message by filling one column after the other, we rearrange the columns based on the key and then read the rows. 

In other words, we write by columns and we read by rows. Also notice that we ignored all the space in the plaintext in this example.  
The resulting ciphertext “NPCOTGHOTH…” is read one row after the other. In other words, a transposition cipher simply rearranges the order of the letters, unlike the substitution cipher, which substitutes the letters without changing their order.

![[Pasted image 20241218112812.png]]

Steps: Given a plaintext P, we:
- **Remove spaces** to make it a continuous string.
- Organize the string into a matrix M with m rows and n columns, where n is determined by the key length.
- **Matrix dimensions**:
$$m = \frac{\text{Length of PlainText}}{n}$$

Write the characters **column by column** into M:
$$
M =
\begin{bmatrix}
a_{1,1} & a_{1,2} & \dots & a_{1,n} \\
a_{2,1} & a_{2,2} & \dots & a_{2,n} \\
\vdots  & \vdots  & \ddots & \vdots  \\
a_{m,1} & a_{m,2} & \dots & a_{m,n}
\end{bmatrix}
$$
- $a_{ij}$ is the character at row i, column j.
- M is filled column-wise.

On Our Example

- Remove spaces: 
  text{"THISISALPHABRAVOCONTACTINGTANGOHOTELMIKE"} 
- Let the key \( K = [4, 2, 3, 5, 1] \) determine \( n = 5 \) columns.  

Write the plaintext column by column into a matrix \( M \):

$$
M =
\begin{bmatrix}
T & P & C & N & O \\
H & H & O & G & T \\
I & A & N & T & E \\
S & B & T & A & L \\
I & R & A & N & M \\
S & A & C & G & I \\
A & V & T & O & K \\
L & O & I & H & E
\end{bmatrix}
$$

---

## 3. Rearranging the Columns Using the Key

The key \( K = [4, 2, 3, 5, 1] \) tells us to rearrange the columns in the order:

text{Original column indices: } [1, 2, 3, 4, 5] New order:  [4, 2, 3, 5, 1]


This column rearrangement can be represented as a **permutation matrix \( P \)**:

$$
P =
\begin{bmatrix}
0 & 0 & 0 & 1 & 0 \\
0 & 1 & 0 & 0 & 0 \\
0 & 0 & 1 & 0 & 0 \\
0 & 0 & 0 & 0 & 1 \\
1 & 0 & 0 & 0 & 0
\end{bmatrix}
$$

Multiply the plaintext matrix \( M \) by the permutation matrix \( P \) to rearrange the columns:
$$
M' = M \cdot P
$$

The resulting matrix \( M' \) is:

$$
M' =
\begin{bmatrix}
N & P & C & O & T \\
G & H & O & T & H \\
T & A & N & E & I \\
A & B & T & L & S \\
N & R & A & M & I \\
G & A & C & I & S \\
O & V & T & K & A \\
H & O & I & E & L
\end{bmatrix}
$$

---

## Extracting the Ciphertext

Read the rows of \( M' \) sequentially (left-to-right) to form the ciphertext \( C \):

$$
C = \text{NPCOTGHOTHTANEIABTLSNRAMIGACISOVTKAHOIEL}
$$

---

## Summary of Steps

1. **Matrix Setup**: Write the plaintext into a matrix \( M \), column by column.
2. **Column Rearrangement**: Rearrange the columns based on the key \( K \) using a permutation matrix \( P \).
3. **Ciphertext Extraction**: Read the rows of the rearranged matrix \( M' \) sequentially to form the ciphertext \( C \).

---
# **Hashing**

**A cryptographic hash function is an algorithm that takes data of arbitrary size as its input and returns a fixed size value**

### What Are Hashes?

Hashes are like digital fingerprints for data. They take the contents of a file or input, run it through a mathematical formula (called a **hash algorithm**), and produce a fixed-length string. This unique string, called a **hash value**, changes completely if even one small part of the data is modified.

Hashes are widely used to:

- **Verify File Integrity:** Check if two files are identical by comparing their hash values.
- **Obscure Passwords:** Store hashes of passwords instead of the actual passwords.

##### File Integrity Commands

Linux Command
```bash
sha256sum
```

Windows PowerShell Command
```powershell
Get-FileHash
```

---

### **How Hashing Works**

1. **Input Data:** Any data (e.g., text or files) is fed into a hash function.
2. **Hash Calculation:** The function processes the data and produces a fixed-size output.
3. **Output Hash:** The resulting hash value is unique for the input data and changes drastically if the input changes.

For example:

- Hashing the word **HELLO** with the **MD5 algorithm** produces a 128-bit hash.
- Adding a small change (like an exclamation mark to make **HELLO!**) results in a completely different hash.

---

### **Common Hash Algorithms**

#### 1. **MD5 (Message Digest 5):**

- **Length:** 128 bits (32 hexadecimal characters).
- **Usage:** Used for file integrity checks and fingerprinting malware samples.
- **Weaknesses:** Known to have **collisions**, meaning two different inputs can sometimes produce the same hash. This makes it less secure for critical applications like password storage.

#### 2. **SHA-1 (Secure Hash Algorithm 1):**

- **Length:** 160 bits (40 hexadecimal characters).
- **Improvement on MD5:** More secure, but researchers discovered **collisions** in 2017.
- **Current Usage:** Many are transitioning away from SHA-1 due to its vulnerabilities.

#### 3. **SHA-2 (Secure Hash Algorithm 2):**

- **Lengths:** Commonly 256 bits (64 characters) and 512 bits (128 characters).
- **Security:** Much stronger than MD5 and SHA-1.
- **Usage:** Often used for secure applications like digital certificates and file integrity.

#### 4. **SHA-3 (Secure Hash Algorithm 3):**

- **Approval:** Approved by the National Institute of Standards and Technology (NIST).
- **Adoption:** Rarely used since SHA-2 is still considered highly secure.
- **Future-Proof:** Serves as a backup if SHA-2 is ever compromised.

---

### **MD5 Hash Mathematical Explanation**

## MD5 Hash Function: Detailed Workflow and Mathematical Explanation

## Overview

MD5 is a cryptographic hash function that processes input data of arbitrary length and produces a fixed 128-bit (16-byte) hash. It is widely used for data integrity verification but is no longer considered secure for cryptographic purposes.

This explanation covers the entire process, including the mathematical operations, from input to the final hash output.

---
## Steps in the MD5 Algorithm

### 1. Input Padding

The input message \( M \) is padded to ensure its length is a multiple of 512 bits.

#### Padding Rules:
1. **Append a single `1` bit**: This is always added to the end of the message.
2. **Add `0` bits**: Append enough `0` bits so that the total length (in bits) is congruent to ( 448 mod 512 ). This leaves 64 bits at the end of the padded message for the length.
3. **Append the length**: The original length of the message (in bits) is added as a 64-bit little-endian integer.

#### Example:
For the input message \( M = text{"abc"} \):
1. The binary representation of `"abc"` is:

```plaintext
   01100001 01100010 01100011
```

(24 bits for "abc") 2. Append a single `1` bit:
```plaintext
01100001 01100010 01100011 1
```

Add `0` bits to make the length ≡448mod512:
```plaintext
01100001 01100010 01100011 10000000... (many zeros)
```

Append the original length (24 bits) as a 64-bit binary number:
```plaintext
...00011000 00000000 00000000 00000000 (total padded message)
```

The result is a single 512-bit block.

## MD5 Hash Function: Detailed Workflow and Mathematical Explanation

## Divide the Input into Blocks
The padded message \( M \) is split into \( k \) blocks, where each block is 512 bits:
$$
M = \{M_1, M_2, \dots, M_k\}
$$
Each block \( M_i \) is further divided into 16 32-bit words:
$$
M_i = \{M_0, M_1, M_2, \dots, M_{15}\}
$$

## Initialize MD5 State Variables
MD5 uses four 32-bit buffers to store intermediate and final results:
$$
A = 0x67452301, \quad B = 0xEFCDAB89, \quad C = 0x98BADCFE, \quad D = 0x10325476
$$
These constants are derived from the fractional parts of the square roots of the first four prime numbers.

## Process Each 512-Bit Block
For each 512-bit block \( M_i \), MD5 processes the data through 64 iterations, grouped into four rounds of 16 steps.

### Non-Linear Functions
MD5 uses four logical functions that operate on the state variables \( B, C, D \):
$$
F(B, C, D) = (B \land C) \lor (\neg B \land D)
$$
$$
G(B, C, D) = (B \land D) \lor (C \land \neg D)
$$
$$
H(B, C, D) = B \oplus C \oplus D
$$
$$
I(B, C, D) = C \oplus (B \lor \neg D)
$$

### Constants
MD5 uses 64 predefined constants \( K[i] \), derived from the sine function:
$$
K[i] = \lfloor 2^{32} \cdot |\sin(i)| \rfloor
$$
Where ( i = 1, 2, ...,  64 ).

### Circular Left Shifts
Each step involves a circular left shift of bits:
$$
\text{ROL}(x, s) = (x \ll s) \lor (x \gg (32 - s))
$$
##### How It Works
![[Pasted image 20241220090900.png]]

**Input**: A 32-bit number and a shift value.

- For example, a number x=11010011 00101101 11101000 10101010 and a shift value s=5.
- The bits of the number are moved to the left by s positions.
- Any bits that move past the leftmost end are "dropped" temporarily.
- Empty positions on the right are filled with zeros.
- Example (5-bit left shift)

```
11010011 00101101 11101000 10101010 ≪5 = 10011001 01101111 01000101 010100000
```
- Example (5 **Right Shift**)

![[Pasted image 20241220091128.png]]
```
11010011 00101101 11101000 10101010 → 00000 00000 00000 00000 00000 11010
```

Where \( s \) is the step-specific rotation amount.

Combine both Results:
```plaintext
10011001 01101111 01000101 01010000 ∨ 00000 00000 00000 00000 00000 11010 = 10011001 01101111 01000101 01011010
```

### Update Variables

At each step \( i \), MD5 updates the buffers \( A, B, C, D \) as follows:

$$
A = B + \text{ROL}(A + F(B, C, D) + M[k] + K[i], s)
$$
Where:
- \( F \) is the logical function for the current round.
- \( M[k] \) is the \( k \)-th word of the current block.
- \( K[i] \) is the round-specific constant.
- \( s \) is the step-specific rotation amount.

After each step, the variables are rotated:
(A, B, C, D) = (D, A, B, C)
This process repeats for all 64 steps in each block.

## Final Combination

After all blocks are processed, the state variables ( A, B, C, D ) are combined with their initial values to produce the final hash:

$$
H_A = A_0 + A_k, \quad H_B = B_0 + B_k, \quad H_C = C_0 + C_k, \quad H_D = D_0 + D_k
$$

The final MD5 hash is the concatenation of these results:

$$
text{MD5 Hash} = H_A \| H_B \| H_C \| H_D
$$
## Example: Input "abc"
### Padding
Original binary:
```plaintext
01100001 \, 01100010 \, 01100011
```

Padded to 512 bits with `1`, `0`s, and message length.

### Initialization
$$
A = 0x67452301, \quad B = 0xEFCDAB89, \quad C = 0x98BADCFE, \quad D = 0x10325476
$$

### Processing
Apply 64 iterations using ( F, G, H, I ), constants ( K[i] ), and shifts (s).

### Final Output
MD5 Hash of "abc":
```plaintext
900150983cd24fb0d6963f7d28e17f72
```

### Hash Commands on Kali

File Integrity Check
```bash
sha256sum filename
```

Text Input sha256
```bash
echo -n "string" | sha256sum
```

Text Input md5sum
```bash
echo -n "string" | md5sum
```

Text Input sha512
```bash
echo -n "string" | sha512sum
```

Text Input sha1
```bash
echo -n "string" | sha1sum
```

### Python with Hash

```bash
pip install hashlib
```

Python's `hashlib` module provides built-in support for MD5, SHA-1, and SHA-2 (SHA-256, SHA-512).

```python
import hashlib

def hash_md5(input_string):
    """Hash a string using MD5."""
    return hashlib.md5(input_string.encode()).hexdigest()

def hash_sha1(input_string):
    """Hash a string using SHA-1."""
    return hashlib.sha1(input_string.encode()).hexdigest()

def hash_sha2(input_string):
    """Hash a string using SHA-256 (SHA-2 family)."""
    return hashlib.sha256(input_string.encode()).hexdigest()
```

Python's file Integrity check

```python
import hashlib

def hash_file_sha256(file_path):
    """
    Hash a file using SHA-256 (SHA-2 family).
    :param file_path: Path to the file to be hashed
    :return: Hexadecimal SHA-256 hash of the file
    """
    hash_sha256 = hashlib.sha256()
    try:
        with open(file_path, "rb") as file:
            while chunk := file.read(8192):  # Read in chunks of 8KB
                hash_sha256.update(chunk)
        return hash_sha256.hexdigest()
    except FileNotFoundError:
        return "Error: File not found."
    except Exception as e:
        return f"Error: {e}"

if __name__ == "__main__":
    # Prompt the user for a file path
    file_path = input("Enter the file path: ").strip()

    # Calculate and print the SHA-256 hash
    sha256_hash = hash_file_sha256(file_path)
    if "Error" in sha256_hash:
        print(sha256_hash)  # Print error message if there's an issue
    else:
        print(f"SHA-256 Hash of the file '{file_path}':\n{sha256_hash}")
```

____
# **HMAC**

### **Understanding HMAC (Hash-Based Message Authentication Code) in Detail**

An **HMAC** (Hash-Based Message Authentication Code) is a method used to ensure the **integrity** and **authenticity** of a message. It combines a cryptographic hash function (e.g., SHA-256) with a secret key. The result of the HMAC calculation is a fixed-size hash value that can verify whether the data has been altered and confirm that it comes from a trusted source with access to the secret key.

---

### **What Does HMAC Solve?**

1. **Integrity:** Ensures that the message hasn't been tampered with during transmission.
2. **Authentication:** Confirms the message came from someone who knows the shared secret key.

HMAC is widely used in secure protocols like HTTPS, TLS, and API authentication systems.

### **How HMAC Works**

HMAC combines a secret key with a message and a hash function through a series of steps to produce a secure hash.

---

## Components of HMAC

To calculate an HMAC, the following components are required:

1. **Hash Function**: A cryptographic hash function like SHA-1, SHA-256, or SHA-512.
2. **Secret Key**: A unique key shared between the sender and receiver.
3. **Inner Padding (ipad)**: A constant byte string (0x36 repeated `B` times), where `B` is the block size of the hash function (e.g., 64 bytes for SHA-256).
4. **Outer Padding (opad)**: Another constant byte string (0x5C repeated `B` times).

---
## Steps to Calculate HMAC

1. **Prepare the Key**:
   - If the key length is less than the hash block size (`B`), pad it with zeroes to make it equal to `B`.
   - If the key length is greater than `B`, hash the key and use the result.

![[Pasted image 20241224020448.png]]

2. **Compute the Inner Hash**:
   - XOR the key with the `ipad`:
$$
     K_{\text{inner}} = K \oplus \text{ipad}
     $$
   - Append the message to this result:
$$
     \text{Inner Input} = K_{\text{inner}} \parallel \text{message}
     $$
   - Hash the result:
$$
     \text{Inner Hash} = H(K_{\text{inner}} \parallel \text{message})
     $$
3. **Compute the Outer Hash**:
   - XOR the key with the `opad`:
$$
     K_{\text{outer}} = K \oplus \text{opad}
     $$
   - Append the inner hash:
$$
     \text{Outer Input} = K_{\text{outer}} \parallel \text{Inner Hash}
     $$
   - Hash the result:
$$
     \text{HMAC} = H(K_{\text{outer}} \parallel \text{Inner Hash})
     $$

**The final HMAC is:**
$$
\text{HMAC} = H(K \oplus \text{opad}, H(K \oplus \text{ipad}, \text{message}))
$$

---
## Example

### Components:
- **Hash Function**: SHA-256
- **Block Size (`B`)**: 64 bytes
- **Secret Key**: `"my_secret_key"`
- **Message**: `"Important Message"`

### Steps:

1. **Prepare the Key**:
   - The key is shorter than 64 bytes, so it's padded with zeroes to make it 64 bytes long.

2. **Compute the Inner Hash**:
   - XOR the padded key with `ipad` (0x36 repeated 64 times).
   - Append the message to the result.
   - Hash the combined data to produce the **inner hash**.

3. **Compute the Outer Hash**:
   - XOR the padded key with `opad` (0x5C repeated 64 times).
   - Append the inner hash to the result.
   - Hash the combined data to produce the final HMAC.

---

## HMAC on Linux

You can calculate HMAC using tools in Linux:

#### Using `hmac256`:

```bash
hmac256 "my_secret_key" message.txt
```

## HMAC with Python

```python
import hmac
import hashlib

def generate_hmac(key, message):
    """
    Generate an HMAC for a given message and key using SHA-256.
    :param key: The secret key as bytes
    :param message: The message to authenticate as bytes
    :return: The HMAC as a hexadecimal string
    """
    return hmac.new(key, message, hashlib.sha256).hexdigest()

def verify_hmac(key, message, expected_hmac):
    """
    Verify an HMAC for a given message and key.
    :param key: The secret key as bytes
    :param message: The message to authenticate as bytes
    :param expected_hmac: The expected HMAC as a hexadecimal string
    :return: True if the HMAC matches, False otherwise
    """
    calculated_hmac = generate_hmac(key, message)
    return hmac.compare_digest(calculated_hmac, expected_hmac)

if __name__ == "__main__":
    # Example usage
    secret_key = b"123456"
    message = b"ahmed"

    # Generate HMAC
    generated_hmac = generate_hmac(secret_key, message)
    print(f"Generated HMAC: {generated_hmac}")

    # Verify HMAC
    is_valid = verify_hmac(secret_key, message, generated_hmac)
    print(f"Is the HMAC valid? {'Yes' if is_valid else 'No'}")
```
### **Advantages of HMAC**

1. **Security:** HMAC combines the strength of cryptographic hash functions with a secret key, making it resistant to tampering.
2. **Efficiency:** It uses hash functions, which are computationally efficient.
3. **Key Dependency:** Without the secret key, it’s almost impossible to forge the HMAC.

### **HMAC vs. Hashing**

|**Aspect**|**Hashing**|**HMAC**|
|---|---|---|
|**Key Usage**|Does not use a key.|Requires a secret key for computation.|
|**Purpose**|Data integrity verification.|Data integrity + authentication.|
|**Vulnerability**|Can be forged if the hash function is weak.|Requires access to the secret key.|

### **Applications of HMAC**

1. **API Authentication:** Ensures requests are authenticated by verifying the HMAC of the message.
2. **Secure Communication:** Used in protocols like HTTPS and TLS to verify data integrity and authenticity.
3. **File Verification:** Ensures files have not been tampered with.

____
# **Diffie-Hellman Key Exchange**

Diffie-Hellman is an asymmetric encryption algorithm. It allows the exchange of a secret over a public channel.

We will skip the modular arithmetic background and provide a simple numeric example. We will need two mathematical operations: power and modulus. _x^p_, i.e., _x_ raised to the power _p_, is _x_ multiplied by itself _p_ times. Furthermore, _x_ mod _m_, i.e., _x_ modulus _m_, is the remainder of the division of _x_ by _m_.

![[Pasted image 20241222030318.png]]

1. Alice and Bob agree on _q_ and _g_. For this to work, _q_ should be a prime number, and _g_ is a number smaller than _q_ that satisfies certain conditions. (In modular arithmetic, _g_ is a generator.) 
   In this example, we take _q_ = 29 and _g_ = 3.
   
2. Alice chooses a random number _a_ smaller than _q_. 
   She calculates _A_ = $g^a$ mod _q_. The number _a_ must be kept a secret; however, _A_ is sent to Bob. 
   Let’s say that Alice picks the number _a_ = 13 
   and calculates _A_ = $3^{13} mod 29 = 19$ and sends it to Bob.
   
3. Bob picks a random number _b_ smaller than _q_. 
   He calculates _B_ = $g^b$ mod _q_. Bob must keep _b_ a secret; however, he sends _B_ to Alice. Let’s consider the case where Bob chooses the number _b_ = 15 and calculates _B_ = $3^{15} mod 29 = 26$. He proceeds to send it to Alice.
   
4. Alice receives _B_ and calculates $key = B^a mod _q$_. 
   Numeric example $key = 26^{13} mod 29 = 10$
   
5. Bob receives A and calculates $key = A^b mod _q$_. 
   Numeric example $key = 19^{15} mod 29 = 10$
   
Although the numbers we have chosen make it easy to find _a_ and _b_, even without using a computer, real-world examples would select a _q_ of 256 bits in length. In decimal numbers, that’s 115 with 75 zeroes to its right (I don’t know how to read that either, but I was told it is read as 115 quattuorvigintillion). Such a large _q_ will make it infeasible to find _a_ or _b_ despite knowledge of _q_, _g_, _A_, and _B_.

We can use `openssl` to generate them; we need to specify the option `dhparam` to indicate that we want to generate Diffie-Hellman parameters along with the specified size in bits, such as `2048` or `4096`

![[Pasted image 20241222031604.png]]

```bash
openssl dhparam -out dhparams.pem 2048
```

In the console output below, we can view the prime number `P` and the generator `G` using the command `openssl dhparam -in dhparams.pem -text -noout`. (This is similar to what we did with the RSA private key.)

```bash
openssl dhparam -in dhparams.pem -text -noout
```

____
# **PKI and SSL/TLS**

Diffie-Hellman is a cryptographic protocol that allows two parties to agree on a shared secret key even if an eavesdropper is present. This shared key is then used in asymmetric encryption to ensure secure communication. However, **Diffie-Hellman is not inherently immune to a Man-in-the-Middle (MITM) attack**.

### **The Vulnerability**
- In the basic Diffie-Hellman exchange, there is no mechanism to verify the identity of the parties.
- An attacker can impersonate one or both parties, intercepting and modifying communications without detection.

---
### **Man-in-the-Middle (MITM) Attack on Diffie-Hellman**

![[Pasted image 20241223013253.png]]

### **Steps of the Attack**

1. **Setup Phase:**
   - Alice and Bob agree on two public parameters:
     - $q$: A large prime number.
     - $g$: A primitive root modulo $q$.
   - These values are transmitted over the communication channel, and **Mallory**, the attacker, observes them.

2. **Interception of Alice’s Key:**
   - Alice generates a random private value $a$.
   - She computes $A = g^a \mod q$ and sends $A$ to Bob.
   - **Mallory intercepts $A$** and generates her own random private value $m$.
   - She computes $M = g^m \mod q$ and sends $M$ to Bob, pretending it is from Alice.

3. **Interception of Bob’s Key:**
   - Bob generates a random private value $b$.
   - He computes $B = g^b \mod q$ and sends $B$ to Alice.
   - **Mallory intercepts \( B \)** and sends \( M \) to Alice instead, pretending it is from Bob.

4. **Key Calculation (Compromised):**
   - Alice computes the shared key as $K_A = M^a \mod q$, assuming $M$ is from Bob.
   - Bob computes the shared key as $K_B = M^b \mod q$, assuming $M$ is from Alice.
   - Mallory computes two keys:
     - $K_MA = A^m \mod q$ (to decrypt messages from Alice).
     - $K_MB = B^m \mod q$ (to decrypt messages from Bob).

5. **Communication (Compromised):**
   - Alice and Bob continue to exchange encrypted messages using their computed keys.
   - Mallory intercepts, decrypts, possibly modifies, and re-encrypts the messages before forwarding them.

---
### **The Need for Identity Verification**

The vulnerability in Diffie-Hellman highlights the need for a mechanism to confirm the identity of the communicating parties. This is where **Public Key Infrastructure (PKI)** comes into play.

---
### **Public Key Infrastructure (PKI) and HTTPS**

#### **Certificate-Based Authentication**
- PKI uses digital certificates to verify the identity of a server or entity.
- For example, when you visit `example.org` over HTTPS:
  1. The server provides its digital certificate.
  2. The certificate contains the server’s public key and is signed by a trusted Certificate Authority (CA).
  3. Your browser verifies the certificate’s validity using the CA’s public key.
  4. If valid, the browser establishes a secure connection.

![[Pasted image 20241223105605.png]]
#### **Secure HTTPS Connection**
- Most browsers indicate a secure connection with a **lock icon**.
- This ensures:
  - The communication is encrypted.
  - You are connected to the legitimate server (e.g., `example.org`), not an impostor.

While protocols like Diffie-Hellman enable secure key exchanges, they are vulnerable without identity verification. PKI, combined with SSL/TLS, mitigates this risk by using certificates to authenticate the communicating parties, ensuring both confidentiality and integrity of data transmission.

we can use `openssl` to generate a certificate signing request using the command 

``` bash
openssl req -new -nodes -newkey rsa:4096 -keyout key.pem -out cert.csr
```

We used the following options:
- `req -new` create a new certificate signing request
- `-nodes` save private key without a passphrase
- `-newkey` generate a new private key
- `rsa:4096` generate an RSA key of size 4096 bits
- `-keyout` specify where to save the key
- `-out` save the certificate signing request

We have just described how PKI applies to the web and SSL/TLS certificates. A trusted third party is necessary for the system to be scalable.

For testing purposes, we have created a self-signed certificate. For example, the following command will generate a self-signed certificate.

``` bash
openssl req -x509 -newkey -nodes rsa:4096 -keyout key.pem -out cert.pem -sha256 -days 365
```

The `-x509` indicates that we want to generate a self-signed certificate instead of a certificate request. The `-sha256` specifies the use of the SHA-256 digest. It will be valid for one year as we added `-days 365`.

____
## **Authenticating with Passwords**

Let’s explore how we can safeguard passwords as they are saved in a database, i.e., data at rest.

The least secure method would be to save the username and the password in a database. This way, any data breach would expose the users’ passwords. No effort is required beyond reading the database containing the passwords.

| Username  | password   |
| --------- | ---------- |
| `alice`   | `qwerty`   |
| `bob`     | `dragon`   |
| `charlie` | `princess` |

The improved approach would be to save the username and a hashed version of the password in a database. This way, a data breach will expose the hashed versions of the passwords. Since a hash function is irreversible, the attacker needs to keep trying different passwords to find the one that would result in the same hash. The table below shows the MD5 sum of the passwords.

|Username|Hash(Password)|
|---|---|
|`alice`|`d8578edf8458ce06fbc5bb76a58c5ca4`|
|`bob`|`8621ffdbc5698829397d97767ac13db3`|
|`charlie`|`8afa847f50a716e64932d995c8e7435a`|

The previous approach looks secure; however, the availability of rainbow tables has made this approach insecure.

A **rainbow table** contains a list of passwords along with their hash value.

Hence, the attacker only needs to look up the hash to recover the password. For example, it would be easy to look up `d8578edf8458ce06fbc5bb76a58c5ca4` to discover the original password of `alice`. Consequently, we need to find more secure approaches to save passwords securely; we can add salt.

A **salt** is a random value we can append to the password before hashing it. An example is shown below.

|Username|Hash(Password + Salt)|Salt|
|---|---|---|
|`alice`|`8a43db01d06107fcad32f0bcfa651f2f`|`12742`|
|`bob`|`aab2b680e6a1cb43c79180b3d1a38beb`|`22861`|
|`charlie`|`3a40d108a068cdc8e7951b82d312129b`|`16056`|

The table above used `hash(password + salt)`; another approach would be to use `hash(hash(password) + salt)`. Note that we used a relatively small salt along with the MD5 hash function. We should switch to a (more) secure hash function and a large salt for better security if this were an actual setup.

#### **Rainbow Table on Kali**

We can use Hashcat for Rainbow table hash crack

Install hashcat
```bash
sudo apt install hashcat
```

Prepare a Wordlist
```bash
gunzip /usr/share/wordlists/rockyou.txt.gz
```

Crack the Hash
```bash
hashcat -m 0 -a 0 -o cracked.txt your_hashes.txt /usr/share/wordlists/rockyou.txt
```

We can use Also john the Ripper

Install john the Ripper
```bash
sudo apt install john
```

```bash
echo "5d41402abc4b2a76b9719d911017c592" > md5.hash
```

```bash
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt md5.hash
```

```bash
john --show md5.hash
```


Using Python

website: https://www.md5online.org/md5-decrypt.htm

```python
pip install requests
```

```Python
import requests

def md5_decrypt(hash_value):
    url = f"https://md5online.org/api/decrypt/{hash_value}"
    response = requests.get(url)
    if response.status_code == 200:
        return response.text
    else:
        return "Hash not found in database"

hash_value = input("Enter MD5 hash: ")
plaintext = md5_decrypt(hash_value)
print(f"Plaintext: {plaintext}")
```

____
# Cryptography and Data - Example

When logging into a website over HTTPS, several cryptographic mechanisms work together to ensure a secure connection and protect sensitive data like login credentials. Here's a clear breakdown of the process:

---

#### **1. Client Requests the Server’s SSL/TLS Certificate**

- The client (e.g., your browser) initiates a connection with the server and requests its SSL/TLS certificate.
- This certificate is critical for establishing a secure connection.

---

#### **2. Server Sends SSL/TLS Certificate to the Client**

- The server provides its SSL/TLS certificate to the client.
- The certificate includes important information, such as:
    - The server’s public key.
    - The server's domain name.
    - The identity of the certificate authority (CA) that issued the certificate.

---

#### **3. Client Validates the Certificate**

- The client confirms that the certificate is valid. This involves:
    1. **Cryptographic Validation**: Ensuring the certificate is signed by a trusted Certificate Authority (CA).
    2. **Signature Verification**:
        - A hash of the certificate is encrypted with the CA's private key to create the digital signature.
        - The client uses the CA's **public key** (stored in the client’s trusted root CA store) to decrypt the signature.
        - The client compares the decrypted hash with its own hash of the certificate. If they match, the certificate is considered valid.
    3. **Trust Check**: If the CA is not recognized or trusted, the client will display a warning, and the connection will not proceed automatically.

---

#### **4. SSL/TLS Handshake**

- After confirming the certificate is valid, the client and server initiate an **SSL/TLS handshake**.
- During this handshake:
    - The client and server agree on:
        - A **symmetric encryption algorithm** for the session.
        - A **shared secret key** for encrypting subsequent communications.
    - Public-key cryptography (asymmetric encryption) is used to securely exchange this key.

---

#### **5. Sending Login Credentials Over Encrypted Session**

- Once the SSL/TLS handshake is complete, all communication between the client and server is encrypted using the agreed symmetric encryption.
- The client sends login credentials (username and password) securely over this encrypted connection.

---

#### **6. Server Verifies Credentials**

- The server receives the login credentials and checks if they match the stored user data.
- For security:
    - **Password Storage**: Instead of storing plain-text passwords, the server:
        - Hashes the password after appending a random **salt** (a unique value added to the password before hashing).
        - Saves the resulting salted hash in its database.
    - **Why Salt?**: If the database is breached, salted hashes make it extremely difficult for attackers to recover the original passwords, even with precomputed hash tables (e.g., rainbow tables).

____

# End of This section