# Space!
You just need to brute the date:
```python
#!/usr/bin/env python3
import hashlib

def md5(msg:str)->str:
    return hashlib.md5(msg.encode()).hexdigest()

def xor(data:str,key:str) -> str:    
    return ''.join(chr(ord(a) ^ ord(b)) for a,b in zip(data, key))

def decrypt():
    for y in range(0,3000):
        for m in range(0,12):
            for d in range(0, 31):
                key = f"{y:04}-{m+1:02}-{d+1:02}"
                hk = md5(key)
                xs = xor(ct, hk)
                if xs.startswith("NTRLGC{"):
                    print(xs)
                    exit()
```
(NDA: May the force be with you 1337 jedi!)